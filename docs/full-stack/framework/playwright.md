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
playwright install --with-deps chromium
playwright install --with-deps firefox
playwright install --with-deps webkit


# 默认安装目录
~/.cache/ms-playwright

# 指定目录安装
export PLAYWRIGHT_BROWSERS_PATH=$HOME/{{CUSTOM_FOLDER}}
playwright install
```

> NOTE:
> 
> Supported Microsoft Edge channels are: msedge, msedge-beta, msedge-dev, msedge-canary

<br>

### 使用方式
```python
# 同步
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch()
    page = browser.new_page()
    page.goto("http://playwright.dev")
    print(page.title())
    browser.close()


# 异步
import asyncio
from playwright.async_api import async_playwright

async def main():
    async with async_playwright() as p:
        browser = await p.chromium.launch()
        page = await browser.new_page()
        await page.goto("http://playwright.dev")
        print(await page.title())
        await browser.close()

asyncio.run(main())
```

<br>

### 显式等待
```
page.wait_for_timeout(5000) # 推荐
time.sleep(5) # 不推荐
```
>NOTE:
>
> Most likely you don't need to wait manually, since Playwright has auto-waiting. If you still rely on it, you should use page.wait_for_timeout(5000) instead of time.sleep(5) and it is better to not wait for a timeout at all, but sometimes it is useful for debugging. In these cases, use our wait (wait_for_timeout) method instead of the time module. This is because we internally rely on asynchronous operations and when using time.sleep(5) they can't get processed correctly.

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

<br>

### Pytest

- **CLI 参数**
```
--headed:          Run tests in headed mode (default: headless).
--browser:         Run tests in a different browser chromium, firefox, or webkit. It can be specified multiple times (default: all browsers).
--browser-channel: Browser channel to be used.
--slowmo:          Run tests with slow mo.
--device:          Device to be emulated.
--output:          Directory for artifacts produced by tests (default: test-results).
--tracing:         Whether to record a trace for each test. on, off, or retain-on-failure (default: off).
--video:           Whether to record video for each test. on, off, or retain-on-failure (default: off).
--screenshot:      Whether to automatically capture a screenshot after each test. on, off, or only-on-failure (default: off).
--base-url:        Start Pytest with the base-url argument to short goto url parameter value
```

<br>

- **Fixture**
```
Function scope: These fixtures are created when requested in a test function and destroyed when the test ends.

context: New browser context for a test.
page:    New browser page for a test.
```
```
Session scope: These fixtures are created when requested in a test function and destroyed when all tests end.

playwright:                         Playwright instance.
browser_type:                       BrowserType instance of the current browser.
browser:                            Browser instance launched by Playwright.
browser_name:                       Browser name as string.
browser_channel:                    Browser channel as string.
is_chromium, is_webkit, is_firefox: Booleans for the respective browser types.
```
```
Customizing fixture options: For browser and context fixtures, use the following fixtures to define custom launch options.

browser_type_launch_args: Override launch arguments for browser_type.launch(**kwargs). It should return a Dict.
browser_context_args:     Override the options for browser.new_context(**kwargs). It should return a Dict.
```

<br>

- Mark
```
@pytest.mark.skip_browser("firefox")
@pytest.mark.only_browser("chromium")
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

> **浏览器控制台调试**:
> 
> playwright.$(selector)       => Query Playwright selector, using the actual Playwright query engine
> 
> playwright.$$(selector)      => Same as playwright.$, but returns all matching elements.
> 
> playwright.inspect(selector) => Reveal element in the Elements panel (if DevTools of the respective browser supports it).
> 
> playwright.locator(selector) => Query Playwright element using the actual Playwright query engine
> 
> playwright.selector(element) => Generates selector for the given element.


<br>

方式五：pdb 调试
```
breakpoint()
```

<br>

方式六：api调试
```
DEBUG=pw:api
pytest -s
```


<br>

方式七：--debug
```
pytest --debug
```

### 常用命令

```console
playwright open playwright.dev

playwright codegen playwright.dev
playwright codegen playwright.dev --save-storage=auth.json
playwright codegen playwright.dev --load-storage=auth.json
playwright codegen playwright.dev --viewport-size=800,600
playwright codegen playwright.dev --device="iPhone 11"
playwright codegen playwright.dev --color-scheme=dark

playwright open --device="iPhone 11" wikipedia.org
playwright open --viewport-size=800,600 --color-scheme=dark twitter.com
playwright open --timezone="Europe/Rome" --geolocation="41.890221,12.492348" --lang="it-IT" maps.google.com

playwright screenshot --help
playwright screenshot --device="iPhone 11" --color-scheme=dark --wait-for-timeout=3000 twitter.com twitter-iphone.png
playwright screenshot --full-page en.wikipedia.org wiki-full.png
playwright pdf https://en.wikipedia.org/wiki/PDF wiki.pdf

pytest --slowmo 100
pytest --browser chromium
pytest --browser-channel chrome
pytest --base-url http://localhost:8080
playwright show-trace trace.zip
pytest --headed -slowmo=2000 --tracing retain-on-failure --video retain-on-failure --screenshot only-on-failure
```

<br>

### [browser.new_context](https://playwright.dev/python/docs/api/class-browser#browser-new-context)
```python
@pytest.fixture(scope="session")
def browser_context_args(browser_context_args, playwright, user_info):
    browser_context_args.update({"record_har_path": "config/record.har", "record_har_url_filter": "**/**"})
    browser_context_args.update({"http_credentials": {"username": "bill", "password": "pa55w0rd"}})
    browser_context_args.update({"proxy": {"server": "http://myproxy.com:3128"}})
    browser_context_args.update({"color_scheme": "dark"})
    browser_context_args.update({"user_agent": 'My user agent'})
    browser_context_args.update(playwright.devices['iPhone 12'])
    browser_context_args.update({"viewport": {"width": 1920, "height": 1080}})
    browser_context_args.update({"locale"="de-DE", "timezone_id"="Europe/Berlin"})
    browser_context_args.update({"permissions": ["notifications"]})
    browser_context_args.update({"geolocation": {"longitude": 48.858455, "latitude": 2.294474}, "permissions": ["geolocation"]})
    browser_context_args.grant_permissions(['notifications'], origin='https://skype.com')
    browser_context_args.clear_permissions()

    storage_state = f"config/storage_{user_info['username']}.json"
    if path.is_exists(storage_state):
        browser_context_args.update({"storage_state": storage_state})

    return browser_context_args
```

<br>

### 元素定位
```python
# Text selector
page.locator('"Login"')
page.locator("text=Log in")      # 模糊匹配，不区分大小写
page.locator("text='Log in'")    # 精确匹配，区分大小写
page.locator("text=/Log\s*in/i") # 正则匹配

# CSS selector
page.locator("button")
page.locator("#nav-bar .contact-us-item")

# Select by attribute, with css selector
page.locator("[aria-label='Sign in']")
page.locator("[data-test=login-button]")
page.locator('css=[placeholder="Search GitHub"]')
page.locator('[placeholder="Search GitHub"]')
page.locator('css=nav >> text=Login')

# Combine css and text selectors
page.locator("button", has_text="Sign up")
page.locator("article:has-text('Playwright')")
page.locator("#simpletable tr:has-text('Raj') td >> nth=3")
page.locator("#nav-bar :text('Contact us')")          # 模糊匹配，不区分大小写
page.locator("#nav-bar :text-is('Contact us')")       # 精确匹配，区分大小写
page.locator("#nav-bar :text-matches('reg?ex', 'i')") # 正则匹配

# Element that contains another, with css selector
page.locator(".item-description:has(.item-promo-banner)")
page.locator("article:has(div.promo)")
page.locator("article", has=page.locator("button.subscribe"))

# Filter
page.locator(".row").locator(":scope", has_text="Hello")

# Selecting elements matching one of the conditions
page.locator('button:has-text("Log in"), button:has-text("Sign in")')
page.locator("//span[contains(@class, 'spinner__loading')]|//div[@id='confirmation']")

# Selecting based on layout, with css selector
page.locator("input:right-of(:text('Username'))")
page.locator("[type=radio]:left-of(:text('Label 3'))")
page.locator("input:above(:text('Username'))")
page.locator("input:below(:text('Username'))")
page.locator("button:near(.promo-card)")
page.locator("button:near(:text('Username'), 120)") # matches a button that is at most 120 pixels away from the element with the text "Username"

# Only visible elements, with css selector
page.locator(".login-button:visible")
page.locator(".login-button >> visible=true")
page.locator("a >> text=Sign up")

# Pick n-th match
page.locator(":nth-match(:text('Buy'), 3)")

# N-th element selector
page.locator("button >> nth=0")

# XPath selector
page.locator("xpath=//button")

# id, data-testid, data-test-id, data-test selectors
page.locator('id=username')
page.locator('data-test-id=submit')

# Parent selector
parent_locator = element_locator.locator('..')

# Chaining selectors
page.locator("tr")
    .filter(has_text="text in column 1")
    .filter(has=page.locator("tr", has_text="column 2 button"))

page.locator("css=article >> css=.bar > .baz >> css=span[attr=value]")
page.locator("css=article >> text=Hello")
page.locator("*css=article >> text=Hello") # Intermediate matches

# Selecting elements in Shadow DOM
page.locator("article div")         # Search for both Light DOM and Shadow DOM
page.locator(":light(article div)") # Search Only for Light
```

<br>

### 元素属性

- 元素位置

```python
page.locator("#position").bounding_box() => {"x": 88, "y": 338, "width": 130, "height": 40}
```

- CSS样式
```python
# 方式一：
page.locator("#color").evaluate("element => {return window.getComputedStyle(element).getPropertyValue('background-color')}")

# 方式二：
expect(page.locator("#color")).to_have_css("background-color", "rgb(138, 77, 118)")
```

### 元素列表
```python
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

### Frame
```python
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
```python
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

<br>

### Table
```python
# 获取整个Table数据
headers = page.locator(".mat-sort th")
rows = page.locator(".mat-sort tr")
cols = page.locator(".mat-sort td")

datas = [[cols.nth(i).text_content() for i in range(j, cols.count(), 6)] for j in range(0, rows.count())]


# 验证列排序
for i in range(headers.count()):
    headers.nth(i).click()
    sort = headers.nth(i).get_attribute("aria-sort")

    if sort == "ascending":
        reverse = False
    elif sort == "descending":
        reverse = True

    data = [
        int(cols.nth(i).text_content()) if cols.nth(i).text_content().isdigit() else cols.nth(i).text_content()
        for i in range(i, cols.count(), 6)
    ]
    assert data == sorted(data, reverse=reverse)


# 查找多次
page.locator("#simpletable tr:has-text('Raj')").locator("input").check()

# 按相对位置查找
page.locator("#simpletable tr:right-of('Raj')").check()


# 抓取整个表格内容（包括分页内容）
page.goto("/advancedtable")
page.wait_for_load_state()

select = page.locator("select")
options = select.locator("option").all_text_contents()

for option in options:
    page.locator("select").select_option(value=[option])
    assert page.locator("#advancedtable tbody tr").count() == int(option)

page.locator("a[data-dt-idx='2']").click()

count = page.locator("#advancedtable th").count()
rows = page.locator("#advancedtable tr")
cols = page.locator("#advancedtable td")
datas = [[cols.nth(i).text_content() for i in range(j, cols.count(), count)] for j in range(0, rows.count())]

# Note: page.locator(".paginate_button.next").is_enabled() is not worked here, looks so strange, so used class attribute instead
while page.locator(".paginate_button.next").get_attribute("class") != "paginate_button next disabled":
    page.locator(".paginate_button.next").click()

    rows = page.locator("#advancedtable tr")
    cols = page.locator("#advancedtable td")
    datas += [[cols.nth(i).text_content() for i in range(j, cols.count(), count)] for j in range(0, rows.count())]
```

<br>

### 上传&下载
```python
# Select one file
page.locator('input#upload').set_input_files('myfile.pdf')

# Select multiple files
page.locator('input#upload').set_input_files(['file1.txt', 'file2.txt'])

# Remove all the selected files
page.locator('input#upload').set_input_files([])

# Upload buffer from memory
page.locator("input#upload").set_input_files(
    files=[
        {"name": "test.txt", "mimeType": "text/plain", "buffer": b"this is a test"}
    ],
)


# Start waiting for the download
with page.expect_download() as download_info:
    # Perform the action that initiates download
    page.locator("button#delayed-download").click()
download = download_info.value
# Wait for the download process to complete
print(download.path())
# Save downloaded file somewhere
download.save_as("/path/to/save/download/at.txt")

page.on("download", lambda download: print(download.path()))
```

<br><br>

### 相关拓展


- [WSLg](https://github.com/microsoft/wslg)
- [jQuery](https://jqueryui.com/demos/)
- [UI自动化练习场](https://letcode.in/test)
- [Playwrights](https://thats-it-code.com/playwright/)
- [Playwright Python 官方文档](https://playwright.dev/python/docs/intro)
- [Key values for keyboard events](https://developer.mozilla.org/en-US/docs/Web/API/UI_Events/Keyboard_event_key_values)
- [playwright-pytest only supports the sync Playwright API at the moment](https://github.com/microsoft/playwright-pytest/issues/74#issuecomment-916001720)
- [Solution: Custom conftest.py for async API support](https://github.com/microsoft/playwright-python/blob/main/tests/async/conftest.py)
- [Playwright with TypeScript, Jest & Playwright test runner](https://www.youtube.com/playlist?list=PL699Xf-_ilW7EyC6lMuU4jelKemmS6KgD)
- [[2022]Playwright with Python and Pytest Full Course](https://www.youtube.com/playlist?list=PLP5_A7hnY1Tggph0F0cRqf5iyyZuIBXYC)