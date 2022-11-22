# Airtest


### 安装
- IDE：https://airtest.netease.com/
- AirTest: pip install airtest
- Poco: pip install pocoui


<br><br>

### 连接设备

- 安卓真机
  1. 用USB线连接电脑和手机设备
  2. 进入手机开发者选项设置，打开设备的USB调试功能（设置 > 系统 > 开发者选项）
  3. 打开Airtest IDE，刷新ADB
  4. 在设备列表中点击 Connect 连接设备

  > NOTE:
  > 1. 连接设备后，Airtest 会自动在目标设备上安装 Yosemite 和 Poco 两个APP
  > 2. adb 目录路径为：AirtestIDE\airtest\core\android\static\adb\windows
  > 3. Yosemite 手动安装目录：AirtestIDE\airtest\core\android\static\apks
  > 4. 建议在目标设备上勾选开发者选项中的“不锁定屏幕”选项，避免目标设备长时间无人操作进入睡眠状态


- 安卓模拟器
  - 雷电模拟器
    1. 勾选远程设备连接
    2. 输入连接字符串：adb connect 127.0.0.1:5554
    3. 点击连接，略做等待后可在设备列表中刷新出来
    4. 选择 emulator-5554,点击连接

- iOS设备
  - 暂时跳过

- Win 窗口
  - 嵌入式
    - Windows 窗口连接 -> 选择游戏画面
    - Windows 窗口连接 -> 搜索窗口
  
  - 非嵌入式（选项 -> 设置 -> Device -> Windows窗口无嵌入式连接）
    - Windows 窗口连接 -> 选择游戏画面
    - Windows 窗口连接 -> 搜索窗口

  - 桌面模式（整个桌面作为一个整体，包含Airtest IDE本身窗口）
    - Windows 窗口连接 -> 连接桌面

  <br>

  > NOTE:
  > 
  > 1. 某些窗口程序可能无法正常嵌入，如Win计算器，此时可以选择无嵌入式连接
  > 2. 窗口嵌入后无法更改大小，此时可以在嵌入前将程序调整为合适的大小再嵌入

<br><br>

### 连接方式

- 命令行中连接设备
  ```
  1.连接安卓真机：
  airtest run D:/test/test.air --device Android://127.0.0.1:5037/{序列号}

  2.连接安卓模拟器
  airtest run D:/test/test.air --device Android://127.0.0.1:5037/emulator-5554?cap_method=JAVACAP&&ori_method=ADBORI

  3.连接Win窗口
  airtest run D:/test/test.air --device Windows:///{窗口句柄}
  ```

- 脚本中连接设备
  ```python
  # 使用 auto_setup 接口连接
  auto_setup(__file__, devices=["Android://127.0.0.1:5037/{序列号}", "Android://127.0.0.1:5037/emulator-5554", "Windows:///{窗口句柄}"])
  auto_setup(__file__, devices=["Windows:///?title_re=吹梦到西州.*"]) # 正则匹配Win窗口，解决句柄动态变更的问题
  auto_setup(__file__, devices=["Windows:///"])                      # 不指定某个具体窗口，等效于嵌入整个桌面的桌面模式


  # 使用 connect_device 接口连接
  dev01 = connect_device("Android://127.0.0.1:5037/{序列号}")
  dev02 = connect_device("Android://127.0.0.1:5037/emulator-5554")
  dev03 = connect_device("Windows:///{窗口句柄}")


  # 使用 init_device 接口连接
  init_device(platform="Android", uuid="{序列号}")
  ```

> NOTE:
> 
> 1. 当模拟器连接有问题时，可逐个尝试以兼容性模式连接(Javacap/ADB Orientation/ADB Touch)
> 2. 在IDE需要勾选备选参数才能够连接上设备时，命令行的设备参数中也要加上这些备选参数; && 符号在Win和MAC系统中都需要转义，否则命令会出现被截断的现象
> 3. Win窗口句柄ID时刻在变化，每次启动窗口生成的句柄ID不同，所以依赖于句柄ID的脚本可能不能复用，此时可以尝试使用正则匹配窗口title的方式解决这一问题
> 4. 小技巧：在IDE中运行脚本，在Log查看器中可以拿到连接设备的string

<br><br>

### AirTest

```python
__author__ = "AirtestProject"
__desc__ = """
纯Airtest脚本--网易云音乐
1.进入网易云音乐
2.找到指定的薛之谦的歌曲
3.回到首页
4.进入抖音歌曲排行榜
5.完整运行上述脚本并统计运行时长
"""

from airtest.core.api import *


import time, datetime

auto_setup(__file__, devices=["Android://127.0.0.1:5037/emulator-5554"])

# 进入网易云音乐
def enter_cloudmusic():
    clear_app("com.netease.cloudmusic")
    start_app("com.netease.cloudmusic")
    wait(Template(r"tpl1598586833881.png", record_pos=(-0.178, -0.314), resolution=(900, 1600)), timeout=50)

    touch(Template(r"tpl1598581158529.png", record_pos=(0.003, 0.209), resolution=(900, 1600)))

    wait(Template(r"tpl1598581590931.png", record_pos=(0.003, -0.447), resolution=(900, 1600)), timeout=50)

    wait(Template(r"tpl1598581676299.png", record_pos=(-0.001, 0.431), resolution=(900, 1600)))

    touch([240, 1535])

    touch(Template(r"tpl1598581275788.png", record_pos=(-0.001, 0.557), resolution=(900, 1600)))

    wait(Template(r"tpl1598581331991.png", record_pos=(-0.051, -0.259), resolution=(900, 1600)))

    assert_exists(Template(r"tpl1598601417614.png", record_pos=(-0.047, -0.239), resolution=(900, 1600)), "进入网易云音乐首页")


#    找到指定的薛之谦的歌曲
def xuezhiqian():

    search = exists(Template(r"tpl1598602706021.png", record_pos=(0.447, -0.778), resolution=(900, 1600)))
    if search:
        touch(Template(r"tpl1598581407163.png", record_pos=(0.444, -0.776), resolution=(900, 1600)))

    text("薛之谦", enter=False)

    wait(Template(r"tpl1598839426750.png", record_pos=(-0.334, -0.659), resolution=(900, 1600)))
    sleep(2.0)

    touch(Template(r"tpl1598602121295.png", record_pos=(-0.332, -0.657), resolution=(900, 1600)))

    sleep(3.0)

    touch(Template(r"tpl1598582289024.png", record_pos=(-0.408, -0.48), resolution=(900, 1600)))

    touch(Template(r"tpl1598582317005.png", target_pos=4, record_pos=(-0.358, -0.078), resolution=(900, 1600)))

    while not exists(Template(r"tpl1598586469967.png", record_pos=(0.018, 0.807), resolution=(900, 1600))):
        swipe([605, 1540], [167, 1540])

    touch(Template(r"tpl1598586571322.png", record_pos=(-0.444, 0.829), resolution=(900, 1600)))

    assert_exists(Template(r"tpl1598601678087.png", record_pos=(-0.3, -0.777), resolution=(900, 1600)), "找到指定歌曲")


# 回到首页
def backto_homepage():

    for i in range(4):
        keyevent("BACK")
    assert_exists(Template(r"tpl1598603817450.png", record_pos=(-0.046, -0.238), resolution=(900, 1600)), "回到首页")


# 进入抖音歌曲排行榜
def douyin_rankinglist():
    rankinglist = exists(Template(r"tpl1598607806791.png", record_pos=(-0.041, -0.24), resolution=(900, 1600)))
    if rankinglist:
        touch(Template(r"tpl1598608855747.png", record_pos=(-0.042, -0.473), resolution=(900, 1600)))

    wait(Template(r"tpl1598608898739.png", record_pos=(-0.391, -0.658), resolution=(900, 1600)))

    while not exists(Template(r"tpl1598608658542.png", record_pos=(-0.394, 0.126), resolution=(900, 1600))):
        swipe([448, 1012], [448, 651])

    touch(Template(r"tpl1598608030711.png", record_pos=(-0.33, 0.124), resolution=(900, 1600)))
    sleep(1.0)
    touch(Template(r"tpl1598608067339.png", record_pos=(0.368, 0.211), resolution=(900, 1600)))

    assert_exists(Template(r"tpl1598839179725.png", record_pos=(-0.019, -0.61), resolution=(900, 1600)), "进入抖音排行榜")
    stop_app("com.netease.cloudmusic")


for i in range(1):
    startTime = datetime.datetime.now()
    try:
        enter_cloudmusic()
        xuezhiqian()
        backto_homepage()
        douyin_rankinglist()
    except:
        print("任务执行失败")

    endTime = datetime.datetime.now()
    # 统计运行总用时，或者在报告中查看
    print("----------------此次用时：" + str((endTime - startTime).seconds) + "s-----------------------")
```

<br><br>

### Poco
- 元素定位
  - 基本选择器：利用元素属性进行定位，如 name/text 等
  - 相对选择器：利用控件之间的父子/爷孙/兄弟关系定位
  - 空间顺序选择器：常用于UI树中多个相同名称的节点定位，根据节点在UI树的索引顺序进行定位

- API
  ```python
  # 点击
  poco("star_single").click()
  poco("star_single").long_click()


  # 读取和设置属性
  poco("star_single").get_name()
  poco("star_single").attr("name")
  poco("star_single").get_text()

  poco("pos_single").set_text("123")
  poco("pos_single").setattr("text", "456")


  # 判断元素是否存在
  poco("xxx").exists()


  # 拖动与滑动
  poco("star").drag_to(poco("shell"))


  # 内部偏移和外部偏移
  poco("pear I").focus([0.1, 0.1]).long_click()
  poco("pear I").focus([0.9, 0.9]).long_click()
  poco("pear I").focus([0.5, -3]).long_click()


  # 遍历元素
  for star in poco("playDragAndDrop").child("star"):
      star.drag_to(poco("shell"))


  # 等待
  poco("bomb").wait_for_appearance()
  poco("bomb").wait_for_disappearance()

  yellow = poco("yellow")
  blue = poco("blue")
  black = poco("black")

  poco.wait_for_all([yellow, blue, black])
  poco.wait_for_any([yellow, blue])
  ```


<br><br>

### airtest-selenium
```python
__author__ = "AirtestProject"

from airtest.core.api import *

from selenium import webdriver
from selenium.webdriver.common.keys import Keys
from airtest_selenium.proxy import WebChrome

# 创建一个实例，代码运行到这里，会打开一个chrome浏览器
driver = WebChrome()
# 设置等待超时，隐式等待，20s
driver.implicitly_wait(20)


auto_setup(__file__)

# 元素定位
driver.find_element_by_xpath("//a[@href='http://map.baidu.com']")
driver.find_element_by_xpath("//input[@type='submit']")

# 录制
driver.find_element_by_xpath("").click()
driver.find_element_by_xpath("//a[@href='http://xueshu.baidu.com']").click()
driver.switch_to_new_tab()
driver.find_element_by_xpath("//*[@id=\"line_one\"]/div/div[2]/div[2]/a/span").click()
driver.switch_to_new_tab()

# 打开指定网址
driver.get("https://www.baidu.com/")

# 元素点击
driver.find_element_by_xpath("//*[@id=\"right_content\"]/div/div/div/div[2]/div/a[4]").click()

# 截图点击
driver.airtest_touch(Template(r"tpl1602301983868.png", record_pos=(13.645, 3.555), resolution=(100, 100)))

# 输入
driver.find_element_by_id("title").send_keys("123")

# 断言元素存在
driver.assert_exist("//div[@alt='到学术首页']", "xpath", "请填写测试点.")

# 断言截图存在
driver.assert_template(Template(r"tpl1602302099427.png", record_pos=(9.335, 1.635), resolution=(100, 100)), "请填写测试点")

# 对当前画面截图
driver.snapshot()

# 标签页切换
driver.switch_to_previous_tab() # 切换到上一个打开的标签页
driver.switch_to_new_tab() # 切换到刚打开的标签页

# 浏览器的后退和前进操作
driver.back()
driver.forward()
```

<br><br>

### 命令行
```
airtest run:

--device: 指定连接的被测设备
--log: 指定log内容和截图存放的目录
--recording: 运行脚本时进行录屏操作
--no-image: 在脚本运行过程中不保存截图


完整示例：
airtest run D:/test/report_test.air --device Android:/// --log D:/log/ --recording D:/log/record01.mp4



airtest report:

--log_root: 指定log内容和截图文件所在目录
--outfile: 指定生成报告的目录
--lang: 指定报告语言，可以是中文/英文
--export: 导出一个包含所有资源的报告文件
--static_root: 指定静态资源文件的路径
--plugins: 指定使用的插件

完整示例：
airtest report D:/test/report_test.air --log_root D:/log --outfile D:/log/log.html --export D:/export --lang zh --plugins poco.utils.airtest.report

NOTE:
1.poco类型脚本需要搭配 --plugins poco.utils.airtest.report 插件使用，否则部分操作可能不会正常显示
```

<br><br>

### 拓展阅读
- [AirtestProject官方文档](https://airtest.doc.io.netease.com/)
- [Android连接常见问题](https://airtest.doc.io.netease.com/IDEdocs/device_connection/2_android_faq/)
- [Android手机助手](https://mp.weixin.qq.com/s/EH0aQnr2AwG0MmFdgoE7mw)
- [常用模拟器连接](https://mp.weixin.qq.com/s/us4Jr9t21nNyKs-bGD9yyg)
- [14天Airtest自动化测试小白课程](https://space.bilibili.com/1403312953)
- [poco的元素定位](https://mp.weixin.qq.com/s/PYI-kGWZCpoaxe2Tmw5d5Q)