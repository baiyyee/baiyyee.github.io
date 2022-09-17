# playwright


### 安装

- 安装 playwright
```
pip install playwright
```

- 安装 pytest-playwright 插件
```
pip install pytest-playwright
```

- 安装浏览器
```
# 完全安装
playwright install

# 指定浏览器安装
playwright install chromium
playwright install firefox
playwright install webkit


# 默认安装目录
~/.cache/ms-playwright

# 指定目录安装
export PLAYWRIGHT_BROWSERS_PATH=$HOME/{{CUSTOM_FOLDER}}
playwright install
```

<br>

### 故障排除


```
playwright codegen https://www.baidu.com
```

>**NOTE**:
>
> 1. 在 WSL 环境中执行以上命令时会提示 `Looks like you launched a headed browser without having a XServer running`，此时只需要以管理员身份执行 `wsl --update` 更新WSL即可。详见：[Is there any plan to suport UI automation with WSL2 environment?](https://github.com/microsoft/playwright/issues/13995)
> 
> 2. 当出现 `Host system is missing dependencies to run browsers` 报错信息时，请尝试执行 `playwright install-deps` 来安装相关依赖。
```
# 缺少XServer支持
╔══════════════════════════════════════════════════════════════════════════════════════════════════════════╗
║ Looks like you lanched a headed browser without having a XServer runnig.                        ║
║ Set either 'headless: false' or use 'xvfb-run <your-playwright-app>' before running Playwright. ║
║                                                                                                 ║
║ <3 Playwright Team                                                                              ║
╚══════════════════════════════════════════════════════════════════════════════════════════════════════════╝

# 缺少依赖
╔═══════════════════════════════════════════════════════════╗
║ Host system is missing dependencies to run browsers. ║
║ Please install them with the following command:      ║
║                                                      ║
║     sudo npx playwright install-deps                 ║
║                                                      ║
║ Alternatively, use apt:                              ║
║     sudo apt-get install libatk1.0-0\                ║
║         libatk-bridge2.0-0\                          ║
║         libpango-1.0-0\                              ║
║         libcairo2\                                   ║
║         libatspi2.0-0                                ║
║                                                      ║
║ <3 Playwright Team                                   ║
╚═══════════════════════════════════════════════════════════╝
```

<br>

### 调试模式

方式一：设置环境变量 PWDEBUG=1
  - 浏览器以有头模式运行
  - 超时时间设置为0，即永不超时


```
export PWDEBUG=1
pytest -s
```

<br>

方式二：设置环境变量 PWDEBUG=console
  - 浏览器以有头模式运行
  - 超时时间设置为0，即永不超时
  - 浏览器控制台调试支持（包含 playwright 对象）
```
export PWDEBUG=console
pytest -s
```

<br>

方式三：在合适位置插入代码
```
sync  => page.pause()
async => await page.pause()
```

<br>

方式四：浏览器启动时启用浏览器开发者工具
```
sync  => chromium.launch(devtools=True)
async => await chromium.launch(devtools=True)
```

<br>

### 常用命令

```console
playwright codegen --save-storage=auth.json
playwright codegen --load-storage=auth.json


pytest --slowmo 100
pytest --browser chromium
pytest --browser-channel chrome
pytest --base-url http://localhost:8080
playwright show-trace trace.zip
pytest --headed -slowmo=2000 --tracing retain-on-failure --video retain-on-failure --screenshot only-on-failure
```


<br><br>

### 相关拓展


- [Playwright Python 官方文档](https://playwright.dev/python/docs/intro)
- [WSLg](https://github.com/microsoft/wslg)
- [playwright-pytest only supports the sync Playwright API at the moment](https://github.com/microsoft/playwright-pytest/issues/74#issuecomment-916001720)
- [Solution: Custom conftest.py for async API support](https://github.com/microsoft/playwright-python/blob/main/tests/async/conftest.py)