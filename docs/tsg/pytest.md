# pytest


### playwright._impl._api_types.Error: Target page, context or browser has been closed

```python
@pytest.hookimpl(tryfirst=True, hookwrapper=True)
def pytest_runtest_makereport(item, call):

    outcome = yield
    rep = outcome.get_result()
    setattr(item, "rep_" + rep.when, rep)


@pytest.fixture(scope="function", autouse=True)
def screenshot_on_failure(request):

    yield

    failed = request.node.rep_call.failed if hasattr(request.node, "rep_call") else True

    if failed:
        for arg in request.node.funcargs.values():
            if isinstance(arg, App):
                allure.attach(body=arg.page.screenshot(), name="screenshot", attachment_type=allure.attachment_type.PNG)
```


**【问题原因】**

page fixture 的范围是 function, 当用例执行完成后，page 会被关闭（参见：[pytest_playwright.py](https://github.com/microsoft/playwright-pytest/blob/main/pytest_playwright/pytest_playwright.py#L202)），所以 `arg.page.screenshot()` 会报异常：playwright._impl._api_types.Error: Target page, context or browser has been closed


**【解决方案】**

方式一：显式调用 `context` fixture, 确保 context 是在截图后才被关闭
```python
@pytest.fixture(scope="function", autouse=True)
def screenshot_on_failure(context, request):

    yield context

    failed = request.node.rep_call.failed if hasattr(request.node, "rep_call") else True

    if failed:
        for arg in request.node.funcargs.values():
            if isinstance(arg, App):
                allure.attach(body=arg.page.screenshot(), name="screenshot", attachment_type=allure.attachment_type.PNG)
```


方式二：网上搜索到了[一个方案](https://blog.csdn.net/henni_719/article/details/125335570)是修改 `pytest_playwright.py` 源码，但这种方式不太友好，仅适用于本地环境，而替代方案就是在 conftest.py 中重写 `context` 来覆盖原来的内容