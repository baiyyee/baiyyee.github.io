# pytest



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