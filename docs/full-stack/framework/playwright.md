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

### 元素定位

- CSS
```
<a _ngcontent-serverapp-c97="" class="button is-primary is-rounded is-small" href="/signup">Sign up</a>
=> a[href='/signup']

<button title="Disabled button" id="isDisabled" disabled="" class="button is-info">Disabled</button>
=> button:has-text('Disabled')
```

- XPath
```
<input id="clearMe" value="Koushik Chatterjee" type="text" placeholder="Enter " class="input">
=> //input[@value='Koushik Chatterjee']
```

- ID
```
<select id="country">
    <option value="Argentina">Argentina</option>
    <option value="Bolivia">Bolivia</option>
    <option value="Brazil">Brazil</option>
    <option value="Chile">Chile</option>
    <option value="Colombia">Colombia</option>
    <option value="Ecuador">Ecuador</option>
    <option value="India">India</option>
    <option value="Paraguay">Paraguay</option>
    <option value="Peru">Peru</option>
    <option value="Suriname">Suriname</option>
    <option value="Uruguay">Uruguay</option>
    <option value="Venezuela">Venezuela</option>
</select>

=> #country
=> page.locator("#lang option").count()
=> page.locator("#lang option").all_text_contents()
```

- Class

```
<div class="notification is-success"><p class="subtitle">You have selected Apple</p></div>
=> div.notification.is-success
```


- Text
```
<button _ngcontent-serverapp-c56="" class="button is-primary">LOGIN</button>
=> text=LOGIN
```

<br>

### 元素属性

- 元素位置

```
page.locator("#position").bounding_box() => {"x": 88, "y": 338, "width": 130, "height": 40}
```

- CSS样式
```
# 方式一：
page.locator("#color").evaluate("element => {return window.getComputedStyle(element).getPropertyValue('background-color')}")

# 方式二：
expect(page.locator("#color")).to_have_css("background-color", "rgb(138, 77, 118)")
```

### 元素列表
```
# Locate elements, this locator points to a list.
rows = page.locator("table tr")

# Pattern 1: use locator methods to calculate text on the whole list.
texts = rows.all_text_contents()

# Pattern 2: do something with each element in the list.
count = rows.count()
for i in range(count):
  print(rows.nth(i).text_content())

# Pattern 3: resolve locator to elements on page and map them to their text content.
# Note: the code inside evaluateAll runs in page, you can call any DOM apis there.
texts = rows.evaluate_all("list => list.map(element => element.textContent)")
```


### Alert

### Frame
```
//iframe

# Locate element inside frame
# Get frame using any other selector
username = page.frame_locator('.frame-class').locator('#username-input')
username.fill('John')


# Get frame using the frame's name attribute
frame = page.frame('frame-login')

# Get frame using frame's URL
frame = page.frame(url=r'.*domain.*')

# Interact with the frame
frame.fill('#username-input', 'John')
```

<br>

### Keyboard
```
await page.keyboard.type('Hello World!');
await page.keyboard.press('ArrowLeft');

await page.keyboard.down('Shift');
for (let i = 0; i < ' World'.length; i++)
  await page.keyboard.press('ArrowLeft');
await page.keyboard.up('Shift');

await page.keyboard.press('Backspace');

await page.keyboard.press('Shift+KeyA');
// or
await page.keyboard.press('Shift+A');

// on Windows and Linux
await page.keyboard.press('Control+A');
// on macOS
await page.keyboard.press('Meta+A');
```

<br><br>

### 相关拓展


- [WSLg](https://github.com/microsoft/wslg)
- [jQuery](https://jqueryui.com/demos/)
- [Playwrights](https://thats-it-code.com/playwright/)
- [Playwright Python 官方文档](https://playwright.dev/python/docs/intro)
- [Key values for keyboard events](https://developer.mozilla.org/en-US/docs/Web/API/UI_Events/Keyboard_event_key_values)
- [playwright-pytest only supports the sync Playwright API at the moment](https://github.com/microsoft/playwright-pytest/issues/74#issuecomment-916001720)
- [Solution: Custom conftest.py for async API support](https://github.com/microsoft/playwright-python/blob/main/tests/async/conftest.py)
- [Playwright with TypeScript, Jest & Playwright test runner](https://www.youtube.com/playlist?list=PL699Xf-_ilW7EyC6lMuU4jelKemmS6KgD)
- [[2022]Playwright with Python and Pytest Full Course](https://www.youtube.com/playlist?list=PLP5_A7hnY1Tggph0F0cRqf5iyyZuIBXYC)