# pytest



### 常用命令

```
pytest Tips：

1.包含测试用例的文件名中包含 test_ 前缀或后缀
2.测试用例方法名包含 test 前缀
3.将所有用例放到某个类中统一管理时，该测试类的类名必须为 TestClass, 否则 pytest 无法识别类里面的测试用例 (collected 0 items )
4.执行包含指定标记的方法(自定义标记在 pytest.ini 文件中定义)
5.可以手动指定文件名或方法名来执行pytest自动识别不了的文件或方法
6.pytest 中的v,k,m 参数可以混合使用


【帮助】
pytest -h                                               # 查看帮助命令



【Discovery】
pytest --collect-only                                   # 发现测试用例，可该方式查看导致 Test discovery failed 的相关问题



【执行】
pytest                                                  # 执行当前目录及其子目录下的所有用例
py.test                                                 # 等效于 pytest
pytest -v                                               # 查看执行用例的详细信息
pytest .\test_calculator.py                             # 执行指定的测试文件
pytest .\test_calculator.py::test_add                   # 执行指定文件中的指定方法

pytest -k "add"                                         # 执行包含add关键字的方法
pytest -k "add or subtract"                             # 执行包含add或包含subtract关键字的方法
pytest -k "add and subtract"                            # 执行包含add且包含subtract关键字的方法

pytest -m "p0"                                          # 执行包含p0标记的方法
pytest -m "not p0"                                      # 执行除p0标记外的其它的方法
pytest -m "p0 or p1"                                    # 执行包含p0或包含p1标记的方法
pytest -m "p0 and bvt"                                  # 执行包含p0且包含bvt标记的方法

pytest -x                                               # 一旦用例出错或是断言失败立即停止执行
pytest --tb=no                                          # 不输出详细调用栈日志信息
pytest --maxfail=2                                      # 一旦用例出错或是断言失败总个数达到2个时立即停止执行
pytest -s                                               # 输出用例中的print信息
pytest --capture=no                                     # 输出用例中的print信息
pytest -q                                               # 安静模式


【日志】
pytest test.py -o log_cli=true -o log_cli_level=INFO    # 覆写pytest.ini文件日志配置


【插件】
pytest --count=2                                        # pytest-repeat => 重复运行2次
pytest --reruns 5 --reruns-delay 1                      # pytest-rerunfailures => Re-run 5次，每次间隔1秒
pytest -n 10                                            # pytest-xdist => 并行执行测试用例


【调试】
pytest --trace tests\demo\test_assert.py                # 使用内置的 pdb 调试测试模块 test_assert.py
pytest --trace tests\demo\test_assert.py::test_assert   # 使用内置的 pdb 调试测试模块 test_assert.py 中的 test_assert 用例

pytest --setup-show                                     # 查看 setup/teardown 执行顺序
pytest --markers                                        # 查看已有的 markers



【测试报告】
pytest -m "unittest" --alluredir=tests\report\allurereport   # 使用allure-pytest插件生成Allure测试报告
allure serve tests\report\allurereport                       # 查看 Allure 报告
allure serve -h 0.0.0.0 -p 8080 ./allure-result              # 指定host和端口

```

<br>

### Build-in Fixure
```python
@pytest.fixture(scope="session", autouse=True)
def delete_output_dir(pytestconfig: Any) -> None:
    output_dir = pytestconfig.getoption("--output")
    if os.path.exists(output_dir):
        try:
            shutil.rmtree(output_dir)
        except FileNotFoundError:
            # When running in parallel, another thread may have already deleted the files
            pass


def pytest_generate_tests(metafunc: Any) -> None:
    if "browser_name" in metafunc.fixturenames:
        browsers = metafunc.config.option.browser or ["chromium"]
        metafunc.parametrize("browser_name", browsers, scope="session")


def pytest_configure(config: Any) -> None:
    config.addinivalue_line(
        "markers", "skip_browser(name): mark test to be skipped a specific browser"
    )
    config.addinivalue_line(
        "markers", "only_browser(name): mark test to run only on a specific browser"
    )


# Making test result information available in fixtures
# https://docs.pytest.org/en/latest/example/simple.html#making-test-result-information-available-in-fixtures
@pytest.hookimpl(tryfirst=True, hookwrapper=True)
def pytest_runtest_makereport(item, call):

    outcome = yield
    rep = outcome.get_result()
    setattr(item, "rep_" + rep.when, rep)


@pytest.fixture(scope="function", autouse=True)
def screenshot_on_failure(context: BrowserContext, request: FixtureRequest):

    yield context

    # If requst.node is missing rep_call, then some error happened during execution
    # that prevented teardown, but should still be counted as a failure
    failed = request.node.rep_call.failed if hasattr(request.node, "rep_call") else True

    if failed:
        for arg in request.node.funcargs.values():
            if isinstance(arg, App):
                allure.attach(body=arg.page.screenshot(), name="screenshot", attachment_type=allure.attachment_type.PNG)


def _get_skiplist(item: Any, values: List[str], value_name: str) -> List[str]:
    skipped_values: List[str] = []
    # Allowlist
    only_marker = item.get_closest_marker(f"only_{value_name}")
    if only_marker:
        skipped_values = values
        skipped_values.remove(only_marker.args[0])

    # Denylist
    skip_marker = item.get_closest_marker(f"skip_{value_name}")
    if skip_marker:
        skipped_values.append(skip_marker.args[0])

    return skipped_values


def pytest_runtest_setup(item: Any) -> None:
    if not hasattr(item, "callspec"):
        return
    browser_name = item.callspec.params.get("browser_name")
    if not browser_name:
        return

    skip_browsers_names = _get_skiplist(
        item, ["chromium", "firefox", "webkit"], "browser"
    )

    if browser_name in skip_browsers_names:
        pytest.skip("skipped for this browser: {}".format(browser_name))


def pytest_addoption(parser):
    parser.addoption(
        "--cmdopt", action="store", default="type1", help="my option: type1 or type2"
    )


@pytest.fixture
def cmdopt(request):
    return request.config.getoption("--cmdopt")
    

def pytest_addoption(parser):
    parser.addini("base_url", help="base url of site under test", default="https://letcode.in")


def pytest_addoption(parser: Any) -> None:
    group = parser.getgroup("playwright", "Playwright")
    group.addoption(
        "--browser",
        action="append",
        default=[],
        help="Browser engine which should be used",
        choices=["chromium", "firefox", "webkit"],
    )
    group.addoption(
        "--headed",
        action="store_true",
        default=False,
        help="Run tests in headed mode.",
    )
    group.addoption(
        "--browser-channel",
        action="store",
        default=None,
        help="Browser channel to be used.",
    )
    group.addoption(
        "--slowmo",
        default=0,
        type=int,
        help="Run tests with slow mo",
    )
    group.addoption(
        "--device", default=None, action="store", help="Device to be emulated."
    )
    group.addoption(
        "--output",
        default="test-results",
        help="Directory for artifacts produced by tests, defaults to test-results.",
    )
    group.addoption(
        "--tracing",
        default="off",
        choices=["on", "off", "retain-on-failure"],
        help="Whether to record a trace for each test.",
    )


@pytest.fixture(scope="session")
def browser_channel(pytestconfig: Any) -> Optional[str]:
    return pytestconfig.getoption("--browser-channel")
```