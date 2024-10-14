# airtest + poco

### 安装
```
pip install -U airtest
pip install -U pocoui
```
> NOTE:
> 
> Poco依赖库是 pocoui 而不是 poco，如果你发现你的环境里面同时存在 poco 和 pocoui ，请务必把 poco 卸载了，留下 pocoui即可



### 设备连接

- 设备连接字符串
    ```
    Android://<adbhost[localhost]>:<adbport[5037]>/<serialno>
    ```

    其中，adbhost 是 adb server 所在主机的 ip ，默认是本机 127.0.0.1 ，adb port 默认是 5037 ， serialno 是 android手机的序列号


    ```
    # 什么都不填写，会默认取当前连接中的第一台手机
    Android:///

    # 连接本机默认端口连的一台设备号为79d03fa的手机
    Android://127.0.0.1:5037/79d03fa

    # 用本机的adb连接一台adb connect过的远程设备，注意10.254.60.1:5555其实是serialno
    Android://127.0.0.1:5037/10.254.60.1:5555


    # 模拟器等特殊设备、使用了连接参数时：

    # 模拟器连接，勾选了Use javacap模式
    Android://127.0.0.1:5037/127.0.0.1:7555?cap_method=JAVACAP

    # 所有的选项都勾选上之后连接的设备，用&&来连接多个参数字符串
    Android://127.0.0.1:5037/79d03fa?cap_method=JAVACAP&&ori_method=ADBORI&&touch_method=ADBTOUCH
    ```

- 连接方式
    ```
    # 方式一：
    auto_setup(__file__, devices=["Android://127.0.0.1:5037/SJE5T17B17"],logdir=True, project_root=r"D:\test\logs", compress=90)

    # 方式二：
    connect_device("Android:///SJE5T17B17?cap_method=javacap&touch_method=adb")

    # 方式三：
    init_device(platform="Android",uuid="SJE5T17B17", cap_method="JAVACAP")
    ```

    示例：
    ```
    from airtest.core.api import *
    device = connect_device("Android://127.0.0.1:5037/JVR6R18915000603")
    poco = AndroidUiautomationPoco(device)
    poco.device.wake()
    ```

- 断开设备
    ```
    dev = connect_device("android:///")
    dev.disconnect() # 能帮助我们主动释放掉一些后面测试不会再用到的资源，比如一些adb连接等
    ```

- 获取本地所有可连接设备
    ```
    from airtest.core.api import *
    from airtest.core.android.adb import *
    auto_setup(__file__)
    adb = ADB()
    device_list = adb.devices()
    ```

### 坐标系
- 归一化坐标系

    Poco的坐标系与Airtest不一样，Airtest使用的是绝对坐标系，在Airtest的脚本里，我们可以传入类似 touch([500,500]) 这样的绝对坐标；但Poco只能接受绝对坐标（归一化坐标系），如上述示例中的 poco.click([0.5,0.5])
    归一化坐标系就是将屏幕宽和高按照单位一来算，这样UI在poco中的宽和高其实就是相对于屏幕的百分比大小了，好处就是不同分辨率设备之间，同一个UI的归一化坐标系下的位置和尺寸是一样的，有助于编写跨设备测试用例。
    归一化坐标系的空间是均匀的，屏幕正中央一定是(0.5, 0.5)，其他标量和向量的计算方法同欧式空间。

    ```
    origin(0, 0)

    0 ---------------->1
    |
    |
    |
    |
    |
    v
    1
    ```

- 绝对坐标与相对坐标互相转换
    ```
    # 获取设备屏幕分辨率(竖屏)
    height = G.DEVICE.display_info['height']
    width = G.DEVICE.display_info['width']

    # 已知绝对坐标[311,1065]，转换成相对坐标
    x1 = 311/width
    y1 = 1065/height
    poco.click([x1,y1])

    # 已知相对坐标[0.3,0.55]，转换成绝对坐标
    x2 = 0.3*width
    y2 = 0.55*height
    touch([x2,y2])

    # 如果是横屏设备的话，则分辨率如下
    height = G.DEVICE.display_info['width']
    width = G.DEVICE.display_info['height']


    # 判断当前屏幕为横屏还是竖屏，并获取当前屏幕的分辨率
    if G.DEVICE.display_info['orientation'] in [1,3]:
        height = G.DEVICE.display_info['width']
        width = G.DEVICE.display_info['height']
    else:
        height = G.DEVICE.display_info['height']
        width = G.DEVICE.display_info['width']
    ```

### 初始化 

- 单台设备
    ```
    from airtest.core.api import *
    from poco.drivers.android.uiautomation import AndroidUiautomationPoco
    auto_setup(__file__,devices=["android://127.0.0.1:5037/127.0.0.1:7555"],logdir=True)
    poco = AndroidUiautomationPoco(use_airtest_input=True, screenshot_each_action=False)
    poco(text="网易云音乐").click()
    ```

    > NOTE:
    > 
    > 1.use_airtest_input=True是指，使用Airtest去执行点击操作(否则是调用Android接口去点击)，好处是，会在日志里面记录一条log记录，这样生成报告时，就能在报告上显示这个点击记录
    > 
    > 2.screenshot_each_action是指，是否开启每个poco步骤的截图


- 多台设备
    ```
    from airtest.core.api import *
    from airtest.core.android import Android
    from poco.drivers.android.uiautomation import AndroidUiautomationPoco
    auto_setup(__file__,devices=["android://127.0.0.1:5037/127.0.0.1:7555","android://127.0.0.1:5037/0123456789"])

    dev1 = Android('127.0.0.1:7555')
    dev2 = Android('0123456789')
    poco1 = AndroidUiautomationPoco(dev1)
    poco2 = AndroidUiautomationPoco(dev2)

    poco1(text="网易云音乐").click()
    poco2(text="网易云音乐").click()
    ```

- 设备初始化顺序
    ```
    from airtest.core.api import *

    # 连接设备
    auto_setup(__file__,logdir=True,devices=["Android://127.0.0.1:5037/emulator-5554?cap_method=JAVACAP"])

    #启动应用
    start_app("com.NetEase")
    sleep(6.0)

    # 初始化poco
    from poco.drivers.unity3d import UnityPoco
    poco = UnityPoco()
    poco("btn_start").click()

    # 获取设备品牌
    brand = subprocess.check_output(['adb', 'shell', 'getprop', 'ro.product.brand'], text=True).strip() 
    # 获取设备型号
    model = subprocess.check_output(['adb', 'shell', 'getprop', 'ro.product.model'], text=True).strip() 
    # 获取设备连接时用到的类
    device().__class__.__name__.lower() => ios/android
    ```
    正确的顺序是：先连接设备（一般在 auto_setup 接口里面连接）--> 再打开游戏应用（一般用 start_app 接口）--> 等应用开启完毕，最后才初始化 poco

    这里简单解释下，为什么我们要先打开游戏应用，再来初始化对应的poco呢？这是因为游戏应用里，事先嵌入了Poco-SDK，当游戏完全启动时，我们的Poco服务才会跟着启动起来，所以我们必须等游戏完全启动（start_app 后面带充足的 sleep ）之后，才能开始初始化游戏的poco。
    而Android和iOS原生应用则没有这一点限制，只要我们先连接上Android/iOS设备之后，就可以开始初始化Android poco或者iOS poco啦


- 多机协作
    ```
    from airtest.core.api import G
    from airtest.core.api import connect_device
    dev1 = connect_device("Android://127.0.0.1:5037/serialno1")  # 连上第一台手机
    dev2 = connect_device("Android://127.0.0.1:5037/serialno2")  # 第二台手机

    print(G.DEVICE_LIST)  # 此时设备列表为[dev1, dev2]

    # 传入数字0切换当前操作的手机到第1台
    set_current(0)

    # 切换当前操作的手机到序列号为serialno2的手机
    set_current("serialno2")

    # 使用device()接口获取当前连接中的设备Android对象
    current_dev = device()


    from airtest.core.api import G
    from poco.drivers.android.uiautomation import AndroidUiautomationPoco
    print(G.DEVICE_LIST)  # 假设当前总共有2台手机
    poco1 = AndroidUiautomationPoco(G.DEVICE_LIST[0])
    poco2 = AndroidUiautomationPoco(G.DEVICE_LIST[1])
    ```

- 使用 python xxxx.py 运行脚本

    直接使用Python运行脚本时，可以采用以下方式：
    ```
    from airtest.core.api import *
    from airtest.cli.parser import cli_setup

    if not cli_setup():
        auto_setup(__file__, logdir=True, devices=[
            "Android:///?cap_method=javacap&ori_method=adbori",
        ])

    # do something
    # touch((x, y))
    ```

    当使用 python xxx.py 来运行本文件，且不带任何命令行参数时，则自动使用 auto_setup 这个接口来对airtest相关的参数进行初始化。这样只需要在写py脚本时，填写好指定的参数就能直接用 python xx.py 指令来运行脚本。
    同时，原先传统的 airtest run xx.air –-devices Android:/// 命令行运行方式也不受影响，只要脚本检测到传入了命令行参数（即代码中的if not cli_setup()判断），就依然优先使用命令行参数来初始化Airtest环境。


### Android APP 常用操作
```
from airtest.core.api import *

# 安装app
install("./com.sina.weibo.apk")
install_app(r"D:\demo\test.apk",install_options=["-r", "-t"])
device().adb.pm_install(APK_FILE)

# NOTE：install 方法需要提供应用的 APK 文件路径，而 install_app 方法则需要提供应用的包名


# 清理app数据
clear_app("org.cocos2d.blackjack")

# 启动APP
start_app("org.cocos2d.blackjack")

# 关闭APP
stop_app("com.sina.weibo")

# 卸载app
uninstall("./com.sina.weibo.apk")

# 切换app
shell("monkey -p com.taobao.taobao -c android.intent.category.LAUNCHER 1")


# 判断apk是否需要覆盖安装
from airtest.utils.apkparser import APK

def need_upgrade(device: Android, package: str, apk_path: str):
    """
    检查手机中的package name的版本号，与本地apk文件版本号进行对比，如果本地文件版本号更高，说明需要覆盖安装
    :param device: Android() 对象
    :param package: 例如：com.netease.cloudmusic
    :param apk_path: 本地apk文件路径
    :return:
    """
    apk_version = int(APK(apk_path).androidversion_code)
    installed_version = device.adb.get_package_version(package)
    if installed_version is None or apk_version > int(installed_version):
        print("local version code is {}, installed version code is {}".format(apk_version, installed_version))
        return True
    return False


# 覆盖安装
from airtest.core.api import *
# 安装参数 -r 表示覆盖安装
install(r"D:\demo\test.apk", install_options=["-r", "-t"])


# 唤醒屏幕
wake()

# 返回HOME
home()

# 文本输入
text("123")

# 截图
snapshot("screen_shot.png")

# 清理后台
dev = device()
#一般Android设备可以从底部向上滑动唤出设备窗
dev.swipe_along([(500, 2295),(500,1500),(500,1000), (500, 100)])
    
#唤出设备窗后，当出现了“关闭所有最近打开的应用”控件，点击即可全部清除所有app后台
if poco("com.huawei.android.launcher:id/clear_all_recents_image_button").exists():
        poco("com.huawei.android.launcher:id/clear_all_recents_image_button").click()



# 传入某个按键响应
keyevent("BACK") # 返回键
keyevent("HOME") # 主页键
keyevent("MENU") # 菜单键
keyevent("VOLUME_UP") # 音量增大
keyevent("VOLUME_DOWN") # 音量减小
keyevent("POWER") # 电源键
keyevent("CAMERA") # 拍照键
keyevent("SEARCH") # 搜索键
keyevent("KEYCODE_DEL") # 删除键


android = Android()
android.path_app("com.netease.cloudmusic") # 返回应用的完整路径
android.check_app("com.netease.cloudmusic") # 检查应用是否存在于当前设备上
android.is_screenon() # 检查屏幕是否打开
android.is_locked() # 检查设备是否锁定
android.get_current_resolution() # 获取当前设备的分辨率
android.get_default_device() # 获取当前设备的序列号
android.list_app(third_only=True) # 列出设备上安装的第三方应用列表


dev = device()  # 获取到当前设备的Android对象
print(dev.get_display_info())  # 查看当前设备的显示信息
print(dev.list_app())  # 打印出当前安装的app列表

# 对当前设备执行指令 adb shell ls
print(shell("ls"))

# 对特定设备执行adb指令
dev = connect_device("Android:///device1")
dev.shell("ls")

# 切换到某台设备，执行adb指令
set_current(0)
shell("ls")
shell("pm list packages -3") # 列出设备上安装的第三方应用列表

# NOTE：实际上，airtest的 shell() ，就是 帮忙补充了 adb -s 手机序列号 shell 这一部分 ，后面的内容就按正常shell指令来传就行了

# 文件操作
from airtest.core.api import *
from airtest.core.android import *
auto_setup(__file__)
​
dev = device()

dev.adb.pull(phone_file,local_path) # 从手机上复制文件到本地
dev.adb.push(local_file,phone_path) # 将本地文件推送到手机
shell("rm %s" % (del_file)) # 删除手机上的文件
```

Airtest封装的安装接口，包含以下安装参数可以使用：

- -t，允许测试包<br>
- -l，锁定应用程序<br>
- -s，把应用程序安装到sd卡上<br>
- -g，为应用程序授予所有运行时的权限<br>
- -r，替换已存在的应用程序（覆盖安装）<br>
- -d，允许降级覆盖安装


### poco 元素定位

- 元素层级关系
  - 子节点：child<br>
  - 所有子节点：children<br>
  - 子孙节点：offspring<br>
  - 父节点：parent<br>
  - 兄弟节点：sibling<br>

- 元素定位
    ```
    poco('bg_mission') # name属性定位
    poco('bg_mission', type='Button') # name + type 定位
    poco('main_node').child('list_item').offspring('item')

    poco(name="淘宝") # 基本选择器
    poco("plays").child("playBasic") # 相对选择器
    poco("Content").child(type="Text")[0] # 空间选择器

    items = poco('main_node').child('list_item').offspring('item')
    print(items[0].child('material_name').get_text())
    print(items[1].child('material_name').get_text())


    poco("android.widget.FrameLayout").child("android.widget.LinearLayout")
    poco("android.widget.FrameLayout").children()[2]


    #【利用子孙节点关系定位元素】统计输出热歌榜当前界面中歌曲数量
    num=len(poco("com.netease.cloudmusic:id/musicInfoList").offspring(nameMatches="com.*?songName"))    
    print("当前界面有{}首歌".format(num))


    #【利用子节点关系定位元素】统计输出当前界面的热门节目单数量
    num=0
    for x in poco("CollectionView").child("UIA.Podcasts.ShelfItem.show"):
        print(x.child().child().get_name())
        num+=1


    #【利用子孙节点关系寻找元素】通过输出子孙节点去查找“发现”按钮
    for i in poco("com.netease.cloudmusic:id/bottomNav").offspring(nameMatches="com.*?desc"):
        print(i.get_text())


    #【利用子孙节点关系以及空间顺序定位元素】点击"发现"按钮
    poco("com.netease.cloudmusic:id/bottomNav").offspring(nameMatches="com.*?desc")[1].click()
    sleep(1.0)


    #【通过兄弟节点关系定位元素】点击发现页中“精选”旁边的“排行榜”按键
    poco("精选").sibling()[1].click()


    #【利用子孙节点关系寻找元素】输出热歌榜中每首歌的名字
    print("当前界面的热歌榜歌曲有：")
    for i in poco("com.netease.cloudmusic:id/musicInfoList").offspring(nameMatches="com.*?songName"):
        print(i.get_text())


    #【利用子孙节点关系定位元素】统计输出热歌榜当前界面中歌曲数量
    num=len(poco("com.netease.cloudmusic:id/musicInfoList").offspring(nameMatches="com.*?songName"))    
    print("当前界面有{}首歌".format(num))


    #【利用子节点关系定位元素】输出目前播放器在播放的歌曲名
    print("下方为正在播放的歌曲：")    
    now_song = poco("com.netease.cloudmusic:id/minibar_song_container").child().children()   
    print(now_song.get_text())


    # 正则定位
    poco(textMatches='^据点.*$', type='Button', enable=True)
    poco(nameMatches=".*portalTitle",textMatches=".*推荐")
    for i in poco(nameMatches="com.*?songInfo"):
        print(i.get_text())
    ```

    除了最常见的 textMatches 、 nameMatches 和 typeMatches ，其实大部分的属性都可以用这种方式来传递正则表达式，只要能够用 poco(xx=预期属性值) 来选择的控件，就可以用 poco(xxMatches=预期属性值的正则表达式) 来进行匹配定位

    Poco控件的定位方式有很多种，但是我们通常不推荐使用相对选择器和控件顺序选择器进行定位，因为复杂的层级关系加上空间索引顺序，很容易出现运行效率差，或者因为索引值变化而导致出现找不到控件的问题。

    我们非常推荐多使用基本选择器和正则匹配表达式来定位控件

    触类旁通，使用1个节点的某个属性值，不仅仅指text属性，还可以是name属性或者其它属性，只要它在当前页面是唯一的，那么我们就可以利用这个属性来定位到我们的控件；甚至 借助节点的多个属性值来进行精准定位控件，都是可以的 。我们非常建议大家多使用这种方式来编写我们的Poco定位脚本。

### poco 常用操作

- 遍历
    ```
    items = poco('main_node').child('list_item').offspring('item')
    for item in items:
        item.child('icn_item')
    ```

- 读取属性
    ```
    poco('bg_mission').attr('type')
    poco('bg_mission').attr('text')
    poco('bg_mission').exists()
    poco('bg_mission').get_text()
    poco("star_single").get_name()
    poco("star_single").get_position() # 获取节点的位置坐标
    poco("star_single").get_bounds() # 获取节点的顶部、右侧、底部和左侧的归一化坐标系
    poco("star_single").get_size()
    ```

- 设置控件的文本信息
    ```
    poco("pos_input").set_text("123")
    poco("pos_input").setattr('text',"456")
    ```

- 删除文本
    ```
    # 利用按键码进行逐字删除
    for i in range(10):
        keyevent("67") # 等同于keyevent("KEYCODE_DEL")

    # 利用Poco的set_text置空进行删除
    poco("com.netease.cloudmusic:id/search_src_text").set_text(" ")
    ```


- 点击
    ```
    poco('bg_mission').click()
    poco('bg_mission').click('center')
    poco('bg_mission').click([0.5, 0.5])    # equivalent to center
    poco('bg_mission').focus([0.5, 0.5]).click()  # equivalent to above expression

    poco("star_single").click() # 控件单击
    poco("star_single").double_click() # 控件双击
    poco('star_single').long_click() # 控件长按
    poco('star_single').long_click(duration=5)
    ```


- 滑动
    ```
    joystick = poco('movetouch_panel').child('point_img')
    joystick.swipe('up')
    joystick.swipe([0.2, -0.2])  # swipe sqrt(0.08) unit distance at 45 degree angle up-and-right
    joystick.swipe([0.2, -0.2], duration=0.5)

    poco("Handle").swipe([0,0.2]) # 向下滑动0.2个单位距离
    poco("Handle").swipe([0,-0.2]) # 向上滑动0.2个单位距离
    poco("Handle").swipe("down") # 向下滑动0.1个单位距离
    poco("Handle").swipe("up") # 向上滑动0.1个单位距离
    ```


- 拖拽
    ```
    poco("playDragAndDrop").child("star")[0].drag_to(poco("shell")) # 拖动到另一个控件上
    poco("playDragAndDrop").child("star")[1].drag_to([0.503, 0.705]) # 拖动到固定目标上

    shell = poco('shell').focus('center')
    for star in poco('star'):
        star.drag_to(shell)
    time.sleep(1)

    # 下拉列表组件里从一个点拖拽到另一个点实现滚动效果
    listView = poco('Scroll View')
    listView.focus([0.5, 0.8]).drag_to(listView.focus([0.5, 0.2]))
    ```


- 滑动手势 => 可用于解锁等复杂手势操作
    ```
    poco = Poco(...)
    ui1 = poco('xxx')
    ui2 = poco('yyy')

    # touch down on ui1 and hold for 1s
    # then drag to ui2 and hold for 1s
    # finally release(touch up)
    ui1.start_gesture().hold(1).to(ui2).hold(1).up()


    from airtest.core.api import *
    from poco.drivers.unity3d import UnityPoco
    auto_setup(__file__)
    poco = UnityPoco()
    # 滑一个V形状
    poco.start_gesture([0.5, 0.5]).hold(1).to([0.6, 0.6]).hold(1).to([0.7, 0.5]).up()
    ```


- 内部偏移
    
    如果选中控件之后，你并不想点击控件的中心位置，而是想点击控件内部的其它位置，我们可以使用 focus() 方法来指定内部偏移量
    ```
    poco('bg_mission').focus('center').click()  # click the center
    poco("star_single").focus([0,0]).click() # 点击星星控件左上角的位置
    poco("star_single").focus([0.5,0.5]).click() # 点击星星控件的中心位置，等同于poco("star_single").click()
    poco("star_single").focus([-0.5,0.5]).click() # 点击星星控件的外部位置
    ```


- 外部偏移

    选中1个控件以后，如果我们想点击控件之外的位置，也可以使用 focus() 方法来指定外部偏移量；并且会出现 点击坐标的值小于0或者大于1 的情况
    ```
    pearl_text = poco(text="pearl")
    pearl_text.focus([0.5,-3]).long_click()

    # 将 focus 和 drag_to 结合使用还能产生卷动(scroll)的效果，下面例子展示了如何将一个列表向上卷动半页
    scrollView = poco(type='ScollView')
    scrollView.focus([0.5, 0.8]).drag_to(scrollView.focus([0.5, 0.2]))
    ```


- 等待
    ```
    poco('bg_mission').wait(5).click()  # wait 5 seconds at most，click once the object appears
    poco('bg_mission').wait(5).exists()  # wait 5 seconds at most，return Exists or Not Exists

    # 在10s内等待控件出现，如出现，则进行长按操作
    poco(texture="icon").wait(timeout=10).long_click() # Poco控件的wait方法，即使在设定时间内未找到控件，也是不会报错的，可以继续往下执行下去

    # 等待黄色小鱼出现
    poco("yellow").wait_for_appearance(timeout=20)
    # 等待计分文本控件消失
    poco(text="Count:").wait_for_disappearance(timeout=3)
    ```

    > NOTE：
    > 
    > wait_for_appearance()和wait_for_disappearance()；这两个API可以帮助我们等待页面上 某1个UI 出现或者消失，等待的超时时间 timeout 默认为120秒，如果在超时时长之内元素没有出现或者消失的话，会报 PocoTargetTimeout 的错误



- 冻结UI
    ```
    poco = UnityPoco()
    # 通过冻结的方式，可以提高约10倍速度，操作的次数越多，提效越多
    with poco.freeze() as frozen_poco:
        for item in frozen_poco('xxx'):
            item.click()
    ```
    poco的所有操作几乎每次操作前，都要通过发送rpc请求把App的hierarchy(UI树)从手机拉到PC，如属性类操作attr()、各种等待、各种点击等，这将非常耗费时间。这么做的原因是Poco无法保证App界面有没有发生变化，所以每次操作前都要去获取实时的hierarchy(UI树)。
    所以Poco提供了freeze()方法，它把hierarchy(UI树)拉取一次后，保存在PC内存中，以后再有交互操作时，不再去手机拉取，而是直接读取PC内存中保存的这份hierarchy(UI树)。这将极大提高操作效率从而节省UI自动化执行时间。

    不过这种方法有一个缺点，就是在你这段操作时间内，UI不能有变化。比如当你点击第一个复选框后，在该复选框下面又多出来一行输入框，那么后面的复选框都要往下移。如果此时仍用保存在PC内存中的hierarchy(UI树)，在你去点击第2个复选框时，实际是点到了新出现的输入框上面。
    所以使用freeze()的场景，一定要确保期间UI不会发生变化。


- 录屏
    ```
    from airtest.core.api import *
    from airtest.core.android.recorder import *
    from airtest.core.android.adb import *

    auto_setup(__file__,devices=["android://127.0.0.1:5037/emulator-5554"])

    adb = ADB(serialno="emulator-5554")
    recorder = Recorder(adb)

    # 开启录屏
    recorder.start_recording(max_time=10)

    touch(Template(r"tpl1603091574169.png", record_pos=(0.113, -0.302), resolution=(900, 1600)))
    sleep(3.0)

    # 结束录屏
    recorder.stop_recording(output="test.mp4")
    ```


- 自定义滑动
    ```
    from airtest.core.api import *
    dev = device()  # 获取当前手机设备
    # 手指按照顺序依次滑过3个坐标
    dev.swipe_along([(100, 100), (200, 200), (300, 300)]) # 滑动解锁
    dev.minitouch.swipe_along([(100, 100), (200, 200), (300, 300)]) # 滑动解锁(请注意这个接口目前只有在使用了默认的 minitouch 模式（Android10使用 maxtouch ）时才能使用)


    # 两个手指同时点击
    from airtest.core.api import *
    from airtest.core.android.touch_methods.base_touch import *
    connect_device("Android:///")

    multitouch_event = [
        DownEvent((100, 100), 0),  # 手指1按下(100, 100)
        DownEvent((200, 200), 1),  # 手指2按下(200, 200)
        SleepEvent(1),
        UpEvent(0), UpEvent(1)]  # 2个手指分别抬起

    device().touch_method.perform(multitouch_event)



    # 使用swipe_along滑个圈
    from airtest.core.api import *

    auto_setup(__file__)

    # 获取当前手机设备
    dev = device()
    # 手指按照顺序依次滑过多个坐标
    dev.swipe_along([[959, 418],[1157, 564],[1044, 824],[751, 638],[945, 415]])
    ```

- 长按删除
    ```
    from airtest.core.android.touch_methods.base_touch import *
    dev = device()

    # 案例一
    # 长按删除应用
    longtouch_event = [
        DownEvent([908, 892]),# 待删除应用的坐标
        SleepEvent(2),
        MoveEvent([165,285]),# 删除应用的垃圾桶坐标
        UpEvent(0)]

    dev.touch_proxy.perform(longtouch_event)

    # 取消卸载
    poco("android:id/button2").click()
    ```


- 多指滑动
    ```
    # 案例三
    # 三指滑动
    swipe_event2 = [DownEvent((100, 300), 0), DownEvent((100, 500), 1), DownEvent((100, 700), 2), SleepEvent(0.1)]

    for i in range(5):
        swipe_event2.append(MoveEvent((100 + 100*i, 300), 0))# 第一根手指
        swipe_event2.append(MoveEvent((100 + 100*i, 500), 1))# 第二根手指
        swipe_event2.append(MoveEvent((100 + 100*i, 700), 2))# 第三根手指
        swipe_event2.append(SleepEvent(0.2))

    swipe_event2.append(UpEvent(0))
    swipe_event2.append(UpEvent(1))
    swipe_event2.append(UpEvent(2))

    dev.touch_proxy.perform(swipe_event2)


    # 双指捏合
    # 获取当前手机设备
    dev = device()


    # 向内捏合
    dev.pinch(in_or_out='in', center=None, percent=0.5)
    sleep(1.0)


    # 向外捏合
    dev.pinch(in_or_out='out', center=None, percent=0.2)
    sleep(1.0)

    dev.pinch(in_or_out='out', center=None, percent=0.2)
    sleep(1.0)
    ```


### 全局操作
```
poco.click([0.5,0.5]) # 单击画面中心位置
poco.long_click([0.5,0.5]) # 长按画面中心位置
poco.swipe([0.9,0.5],[0.3,0.5]) # 向左滑动
frozen_poco = poco.freeze() # 冻结当前的UI树，可提高poco查找效率
poco.use_render_resolution(True, (0 ,100 ,1080 ,2020)) # 自定义渲染分辨率，为更好支持更多不兼容的全面屏设备


# wait_for_all() 是在超时时长结束之前，需要 等待所有给定的UI对象都显示出来 ，即一次轮询所有UI
poco("wait_ui2").click()
yellow = poco("yellow")
blue = poco("blue")
black = poco("black")
poco.wait_for_all([yellow,blue,black])
poco("btn_back").click()


# wait_for_any() 则是在超时时长结束之前，等待任意一个UI显示出来，即一次轮询任何一个给定的UI
bomb = poco("bomb")
yellow = poco("yellow")
blue = poco("blue")
while True:
    fish = poco.wait_for_any([bomb,yellow,blue])
    print(fish.get_name())


# 小游戏：点击蓝色小鱼和黄色小鱼，不能点击炸弹
from poco.drivers.unity3d import UnityPoco
from poco.exceptions import PocoTargetTimeout

poco = UnityPoco()

bomb_count = 0
while True:
    blue_fish = poco('fish_emitter').child('blue')
    yellow_fish = poco('fish_emitter').child('yellow')
    bomb = poco('fish_emitter').child('bomb')
    fish = poco.wait_for_any([blue_fish, yellow_fish, bomb])
    if fish is bomb:
        # skip the bomb and count to 3 to exit
        bomb_count += 1
        if bomb_count > 3:
            return
    else:
        # otherwise click the fish to collect.
        fish.click()
    time.sleep(2.5)



# 滑动
# swipe from A to B
point_a = [0.1, 0.1]
center = [0.5, 0.5]
poco.swipe(point_a, center)

# swipe from A by given direction
direction = [0.1, 0]
poco.swipe(point_a, direction=direction)

# 选中UI执行swipe
# 向上滑动列表视图
poco('Scroll View').swipe([0, -0.1])
# 与上面这条代码一样, 并且还可以传入down/left/right
poco('Scroll View').swipe('up')  
# 向下滑动列表视图
poco('Scroll View').swipe('down')

# 不选中UI执行swipe
x, y = poco('Scroll View').get_position()
end = [x, y - 0.1]
dir = [0, -0.1]
# 从点A滑动到点B
poco.swipe([x, y], end)  
# 从点A沿着一定的方向滑动一段距离   
poco.swipe([x, y], direction=dir) 



# 截屏
from base64 import b64decode
b64img, fmt = poco.snapshot(width=720)
open('screen.{}'.format(fmt), 'wb').write(b64decode(b64img))


from base64 import b64decode
b64img, fmt = poco.snapshot(width=720)
open('screen.{}'.format(fmt), 'wb').write(b64decode(b64img))
```

### 异常处理

- InvalidOperationException
- PocoException
- PocoNoSuchNodeException
- PocoTargetRemovedException
- PocoTargetTimeout


示例：
```
from poco.exceptions import PocoTargetTimeout
try:
    poco('guide_panel', type='ImageView').wait_for_appearance()
except PocoTargetTimeout:
    # bugs here as the panel not shown
    raise


from poco.exceptions import PocoNoSuchNodeException
img = poco('guide_panel', type='ImageView')
try:
    if not img.exists():
        img.click()
except PocoNoSuchNodeException:
    # If attempt to operate inexistent nodes, an exception will be thrown
    pass
```

### 应用授权
```
#允许应用的电话权限
shell("pm grant com.sina.weibo android.permission.READ_PHONE_STATE")

#撤销应用的电话权限
shell("pm revoke com.sina.weibo android.permission.READ_PHONE_STATE")

#允许应用存储的读写权限
shell("pm grant com.sina.weibo android.permission.READ_EXTERNAL_STORAGE")
shell("pm grant com.sina.weibo android.permission.WRITE_EXTERNAL_STORAGE")

#撤销应用存储的读写权限
shell("pm revoke com.sina.weibo android.permission.READ_EXTERNAL_STORAGE")
shell("pm revoke com.sina.weibo android.permission.WRITE_EXTERNAL_STORAGE")
```

其他权限：
- 应用的电话权限：android.permission.READ_PHONE_STATE
- 应用存储的读取权限：android.permission.READ_EXTERNAL_STORAGE
- 应用存储的写入权限：android.permission.WRITE_EXTERNAL_STORAGE
- 应用打开位置权限：android.permission.ACCESS_COARSE_LOCATION
- 应用打开摄像头权限：android.permission.CAMERA
- 应用访问麦克风：android.permission.RECORD_AUDIO
- 应用读取联系人数据：android.permission.READ_CONTACTS
- 应用读取短信数据：android.permission.READ_SMS
- 应用读取设备的日历表：android.permission.READ_CALENDAR
- 应用写入设备的日历表：android.permission.WRITE_CALENDAR



### 控件截图
```
方式一：
from airtest.core.api import *
from airtest.aircv import *

auto_setup(__file__)

from poco.drivers.android.uiautomation import AndroidUiautomationPoco
poco = AndroidUiautomationPoco(use_airtest_input=True, screenshot_each_action=False)

#获取屏幕分辨率
android = device()
xy=android.get_current_resolution()

screen = G.DEVICE.snapshot()

#使用poco去寻找控件
if poco(text="游戏增强器").exists():
    a=poco(text="游戏增强器").get_position()#获取控件的中心坐标
    b=poco(text="游戏增强器").get_size()#获取控件的实际长宽
    
    #计算左上角坐标，转化成绝对坐标
    x1=int((a[0]-0.5*b[0])*xy[0])
    y1=int((a[1]-0.5*b[1])*xy[1])
    
    #计算右下角坐标，转化成绝对坐标
    x2=int((a[0]+0.5*b[0])*xy[0])
    y2=int((a[1]+0.5*b[1])*xy[1])
    
    # 局部截图
    screen = aircv.crop_image(screen,(x1,y1,x2,y2))
    # 保存局部截图到log文件夹中
    try_log_screen(screen)


方式二：
from airtest.core.api import *
from airtest.aircv import *

auto_setup(__file__)
screen = G.DEVICE.snapshot()

# 局部截图
screen = aircv.crop_image(screen,(0,160,1067,551))
# 保存局部截图到log文件夹中
try_log_screen(screen)
```

### 测试报告
- 记录自定义函数到报告
    ```
    from airtest.core.api import *
    auto_setup(__file__)

    @logwrap
    def open_calculator():
        wake()
        start_app("com.sonymobile.exactcalculator")

    open_calculator()


    # 记录日志并附加截图 => snapshot=True
    @logwrap
    def open_calculator(snapshot=True):
        wake()
        start_app("com.sonymobile.exactcalculator")

    ```
- simple_report(filepath, logpath=True, logfile="log.txt", output="log.html") 接口，它其实是1个简化版的生成报告的接口，可以减少同学们的理解成本和使用成本

  - filepath：指定脚本的路径
  - logpath：指定log内容的路径
  - logfile：指定log.txt文件的路径
  - output：指定HTML报告的生成路径，必须以 .html 结尾
    ```
    from airtest.core.api import *
    from airtest.report.report import simple_report
    from poco.drivers.android.uiautomation import AndroidUiautomationPoco

    def demo():
        # 连接设备以及初始化工作 log文件夹默认生成在当面目录下 
        auto_setup(__file__, logdir=True, devices=["Android:///",],)

        # 初始化poco
        poco = AndroidUiautomationPoco(use_airtest_input=True, screenshot_each_action=False)
        # 执行一些操作 确保手机app已登录并跳转到app初始化界面
        try:
            # 以下操作执行：我的-》我喜欢的音乐-》点击播放
            poco(text="我的").click()
            poco(text="我喜欢的音乐").click()
            # 灵活结合ide提供的poco ui树获取播放按钮name属性值 
            poco(name="com.netease.cloudmusic:id/minPlayBtn").click()
        except Exception as err:
            # 记录报错到logdir下的log.txt文件中，并截图保存发生错误时的手机屏幕
            log(err, desc="Finale Error", snapshot=True)
        # 无论运行是否成功 输出报告
        finally:
            # 默认生成html报告到脚本所在的同级目录下
            simple_report(__file__, logpath=True, output="test/app/log.html")

    if __name__ == '__main__':
        demo()
    ```

- class LogToHtml(script_root, log_root="",static_root="",export_dir=None,script_name="",logfile="log.txt",lang="en",plugins=None)

  - script_root，指定脚本路径
  - log_root，指定log文件的路径
  - static_root，指定部署静态资源的服务器路径
  - export_dir，设置导出报告的存放路径
  - script_name，脚本名称
  - logfile，指定log文件log.txt的路径
  - lang，指定报告的语言（中文：zh；英文：en）
  - plugins，指定报告插件，使用了poco或者airtest-selenium会用到
    ```
    from airtest.report.report import LogToHtml
    h1 = LogToHtml(script_root=__file__, log_root="test/app/log", lang="zh", plugins=["poco.utils.airtest.report"])
    h1.report(output_file="test/app/log.html")


    # 自动连接重启后的设备并继续执行自动化脚本
    from airtest.core.api import *
    from airtest.core.android.android import *

    auto_setup(__file__)

    dev = connect_device("android://127.0.0.1:5037/QV720MVQ52")

    from poco.drivers.android.uiautomation import AndroidUiautomationPoco
    poco = AndroidUiautomationPoco(use_airtest_input=True, screenshot_each_action=False)

    # 简单测试网易云音乐APP
    def simple_test():
        clear_app("com.netease.cloudmusic")
        start_app("com.netease.cloudmusic")

        sleep(1.0)

        poco("com.netease.cloudmusic:id/agree").click()
        sleep(1.0)
        poco("com.netease.cloudmusic:id/permissionGrant").click()
        sleep(1.0)
        poco("com.sonymobile.cta:id/permission_allow_button").click()
        sleep(1.0)

        wait(Template(r"tpl1667356693271.png", record_pos=(0.0, -0.688), resolution=(1080, 2520)),timeout=30)

        poco("com.netease.cloudmusic:id/agreeCheckbox").click()
        poco("com.netease.cloudmusic:id/trial").click()
        sleep(1.0)

    # 简单测试后让设备重启
    simple_test()
    dev.shell("reboot")
    sleep(60.0)

    # 这里执行重启后的自动化操作
    log("这里开始执行重启后的自动化操作")

    # 重新连接设备
    connect_device("android://127.0.0.1:5037/QV720MVQ52")

    # 给设备解锁
    android = Android("QV720MVQ52")
    android.unlock()
    swipe([552, 2407],[543, 664])

    # 重新初始化Android poco并进行一些自动化测试
    from poco.drivers.android.uiautomation import AndroidUiautomationPoco
    poco = AndroidUiautomationPoco(use_airtest_input=True, screenshot_each_action=False)
    sleep(3.0)
    simple_test()
    ```

- logdir与logpath

    生成Airtest报告需要依赖脚本运行过程保存的log，所以如需生成报告，就需要保存脚本运行过程的log，auto_setup  接口的logdir参数可以设置log保存路径；使用simple_report生成报告时，就可以设置logpath，到logdir指定的log保存路径下面去找脚本对应的log内容


### Yosemite.apk

在Android平台下，Airtest配了一个专用的输入法 Yosemite.apk，当我们在Android设备上运行到输入文本的接口，比如 text 接口时，会自动启用这个 Yosemite 输入法来进行输入
当Android设备启用 Yosemite 输入法时，我们就只能通过脚本来对设备进行输入，无法手工输入，如果需要手工输入，可以在手机系统的输入法设置中，把输入法切换回系统输入法即可恢复
如果我们确定在测试脚本中，不会涉及任何文本输入的操作；或者是只需要输入英文，并且是以 adb shell input 的方式来进行输入的，我们可以不在手机上安装 Yosemite 输入法
```
# 在脚本中指定不使用 Yosemite 输入法
from airtest.core.api import *
# 方式一
auto_setup(__file__,devices=["android:///?ime_method=ADBIME"])


# 方式二
init_device("Android", ime_method="ADBIME")
```

### Airtest的 text 接口

Airtest框架给我们提供的输入接口是 text 接口，在使用 text 接口之前，我们 必须先点击输入框，激活下输入光标
text 接口有一个默认参数 enter=True，会在输入完毕后自动按一下回车键（相当于 keyevent("ENTER")），假如不需要，请传入 enter=False
```
text("test", enter=False)
```

部分输入框，需要在输入内容后，点击输入键盘上的 搜索 按钮才能够激活搜索操作，可以传入 search=True 参数
```
text("test", search=True)
```

如果我们在使用类似 text("11") 这样的脚本，不能正确回车的时候，我们可以使用下述方式来添加这个回车的效果
```
text("11")
ime=G.DEVICE.yosemite_ime
ime.code(6)
```

text 输入失败，大概率都绕不过下面几个问题：

- 手机阻止了 Yosemite.apk 的安装和运行
- 没有将 Yosemite 输入法设置为手机默认输入法
- 输入账号密码之类的，没有关闭手机的 安全输入 / 安全键盘 设置
- 部分手机的兼容性问题
- 部分模拟器未关闭 物理键盘 的问题

```
# 除了Airtest框架提供的 text 接口，Poco框架也提供了输入的 set_text 接口
poco("com.android.mms:id/recipients_editor").click()
poco("com.android.mms:id/recipients_editor").set_text("test")

poco("com.android.mms:id/recipients_editor").set_text(" ") # 快速清空输入框


# 输入法切换(ADB指定切换某个输入法，需要 先启动，再设置)
adb shell ime enable 输入法ID
adb shell ime set 输入法ID

如下：
adb shell ime enable com.netease.nie.yosemite/.ime.ImeService
adb shell ime set com.netease.nie.yosemite/.ime.ImeService

# 查看当前使用的输入法
adb shell settings get secure default_input_method


# 切换输入法的Python实现
from airtest.core.api import *
auto_setup(__file__)
ym = "com.netease.nie.yosemite/.ime.ImeService"
sh = "com.sohu.inputmethod.sogouoem/.SogouIME"

def set_ime(ime):
    shell("ime enable " + ime)
    shell("ime set " + ime)

set_ime(ym)
text("123", enter=False)
set_ime(sh)


# 获取当前输入法
from airtest.core.api import *
auto_setup(__file__)
dev = device()
print(dev.yosemite_ime._get_ime_list())
```


### 断言
- assert_exists => assert_equal(poco("star_single").exists(),True,"断言星星控件存在")
- assert_not_exists
- assert_equal => assert_equal(poco(xxx).exists(),True,"xxx控件存在")
- assert_not_equal
- assert_true
- assert_false
- assert_is
- assert_is_not
- assert_is_none
- assert_is_not_none
- assert_in
- assert_not_in
- assert_is_instance
- assert_not_is_instance



### 全局变量
在airtest.core.settings里，提供了部分全局默认属性，其中我们列举出几个常见属性和它们的默认值：
- 变量参数
  - LOG_DIR = None # 用于自定义log内容的保存路径
  - LOG_FILE = "log.txt" # 用于自定义记录log内容的txt文档的名称
  - RESIZE_METHOD = staticmethod(cocos_min_strategy) # 分辨率适配规则
  - CVSTRATEGY = ["mstpl", "tpl", "surf", "brisk"] # 图像识别算法
  - THRESHOLD = 0.7 # [0, 1] # 图像识别阙值
  - OPDELAY = 0.1 # 步骤之间的时间间隔
  - FIND_TIMEOUT = 20 # 图片查找超时时长
  - FIND_TIMEOUT_TMP = 3 # 图片查找超时时长
  - PROJECT_ROOT = os.environ.get("PROJECT_ROOT", "") # 项目根目录
  - SNAPSHOT_QUALITY = 10 # 1-100 # 截图压缩精度
  - IMAGE_MAXSIZE = os.environ.get("IMAGE_MAXSIZE", None) # 截图尺寸大小
  - SAVE_IMAGE = True # 保存运行过程的截图

- 全局变量设置方法
    ```
    from airtest.core.api import *
    ST.THRESHOLD = 0.8

    from airtest.core.settings import Settings as ST
    from airtest.core.helper import set_logdir
    ST.LOG_FILE = "log123.txt"
    set_logdir(r'D:\test\1234.air\logs')

    from airtest.core.settings import Settings as ST

    # 设置全局截图尺寸不超过600*600，如果不设置，默认为原图尺寸
    ST.IMAGE_MAXSIZE = 600

    # 不单独设置的情况下，默认采用ST中的全局变量的数值，即600*600
    snapshot(msg="test12")

    # 设置单张截图的最大尺寸不超过1200*1200
    snapshot(filename="test2.png", msg="test02", quality=90, max_size=1200)
    ```



### 常用 adb 命令
```
adb devices # 查看已连接设备
adb version # 查看adb版本
adb shell pm list packages # 查看设备上的所有应用
adb shell pm list packages -3 # 查看设备上的第三方应用
adb shell pm list packages -s # 查看设备上的系统应用
adb install "D:/demo/tutorial-blackjack-release-signed.apk" # 安装APK
adb uninstall com.netease.cloudmusic # 卸载应用
adb shell dumpsys package com.netease.cloudmusic # 查看应用详细信息
adb pull /sdcard/sr.mp4 ~/tmp/ # 复制设备里的文件到电脑
adb push ~/sr.mp4 /sdcard/ # 复制电脑里的文件到设备
adb shell input keyevent 26 # 模拟电源键
adb shell input keyevent 3 # 模拟HOME键
adb shell input keyevent 4 # 模拟返回键
adb shell input keyevent 224 # 点亮屏幕
adb shell input keyevent 223 # 熄灭屏幕
adb shell input swipe 200 1000 200 500 # 模拟滑动解锁（200 1000 200 500 分别表示起始点x坐标 起始点y坐标 结束点x坐标 结束点y坐标）
adb shell input text airtest # 输入文本
adb -s PFT4PBLF75GQHYBM shell getprop ro.product.model # 查看设备号
adb -s PFT4PBLF75GQHYBM shell wm size # 查看设备分辨率
adb -s PFT4PBLF75GQHYBM shell getprop ro.build.version.release # 查看Android系统版本
```
想了解更多详细的ADB操作的同学，我们推荐大家阅读这篇GitHub文档：https://github.com/mzlogin/awesome-adb


### Tips

- 用Poco测试小程序

    微信和QQ内置的浏览器内核不是Android原生的WebView，而是他们自家的 X5 WebView 内核。我们需要切换到系统WebView内核，这样Poco才能更好地识别出小程序的各种UI控件

    如何进行设置：<br>
    ① 打开微信中任意一个聊天框，输入 debugtbs.qq.com，点击进入TBS调试页面，再点击页面内的“安装线上内核”<br>
    ② 安装成功后，我们需要重启微信才可以生效；<br>
    ③ 再次进去刚才的TBS调试页面，检查我们的设置是否生效，点击“查看版本信息”，版本信息为 0(null) 则设置成功<br>

    设置成功以后即可用Poco对小程序进行测试（TBS调试只适用于Android系统，iOS暂不支持）

    示例：
    ```
    from airtest.core.api import *

    from poco.drivers.android.uiautomation import AndroidUiautomationPoco
    poco = AndroidUiautomationPoco(use_airtest_input=True, screenshot_each_action=False)
    auto_setup(__file__)

    # 传入微信的包名启动微信app
    start_app("com.tencent.mm")

    # 下滑进去小程序页面
    swipe((550,250),(550,1200))
    sleep(2.0)

    # 点击下厨房小程序
    poco("com.tencent.mm:id/cud").offspring("com.tencent.mm:id/dai").child("android.widget.RelativeLayout").offspring("com.tencent.mm:id/le").offspring("com.tencent.mm:id/lh").child("android.widget.LinearLayout")[2].offspring("com.tencent.mm:id/eko").child("android.widget.RelativeLayout")[0].offspring("com.tencent.mm:id/cik").click()
    sleep(2.0)

    # 点击搜索框
    poco(text="今天想吃点什么？").click()
    sleep(1.0)

    # 再次点击搜索框
    poco("android.widget.EditText").click()
    sleep(1.0)

    # 输入搜索词“蛋糕”
    poco("android.widget.EditText").set_text("蛋糕")

    # 点击搜索
    poco(text="搜索").click()

    # 打印找到第一个配方的标题
    print(poco(text="超容易上手的古早蛋糕").get_text())
    ```

- 应用swipe跳过APP的介绍页
    ```
    方式一：
    # 获取设备的高度和宽度
    width, height = device().get_current_resolution()
    # 校准滑动的起点和终点
    start_pt = (width * 0.9, height / 2)
    end_pt = (width * 0.1, height / 2)
    # 滑动5次:
    for i in range(5):
        swipe(start_pt, end_pt)
        sleep(1)  # 等待设备的响应

    方式二：按比例滑动
    for i in range(5):
        poco.swipe([0.9,0.5], [0.1,0.5])
        sleep(1)  # 等待设备的响应
    ```

### 场景实践：pytest + airtest + poco

代码demo: pytest + airtest + poco<br>
脚本名称：test_andorid_poco.py<br>
设备类型：荣耀Waterplay<br>
Android版本：7.0<br>
测试APP：华为视频<br>
测试场景：获取华为视频电影Tab下的全部电影名称<br>
运行命令：pytest test/app/test_andorid_poco.py -s<br>
```
import os
import pytest
import logging
from airtest.core.api import *
from airtest.core.android.adb import *
from airtest.core.android.recorder import *
from poco.proxy import UIObjectProxy
from airtest.report.report import LogToHtml
from airtest.report.report import simple_report
from poco.drivers.android.uiautomation import AndroidUiautomationPoco


__author__ = "hehuabo"


# 全局配置每个操作之间的延迟时间
ST.OPDELAY = 1  

# NOTE：操作之间间隔太短可能导致异常 => requests.exceptions.ReadTimeout: HTTPConnectionPool(host='127.0.0.1', port=16583): Read timed out. (read timeout=0.3)


def loop_items(poco: AndroidUiautomationPoco, locater: UIObjectProxy):
    seen_items = set()

    while True:
        items = locater
        new_items_found = False

        for item in items:
            title = item.get_text()
            if title not in seen_items:
                logging.info(title)
                seen_items.add(title)
                new_items_found = True

        if not new_items_found:
            break

        # 模拟屏幕向上滑动
        poco.swipe([0.5, 0.8], [0.5, 0.2], duration=2.0)


@pytest.fixture(scope="session", autouse=True)
def clear_log():
    # 清理日志
    if os.path.exists("test/app/log"):
        os.system("rm -r test/app/log/*")


@pytest.fixture(scope="session", autouse=True)
def poco():
    # device = connect_device("Android://127.0.0.1:5037/JVR6R18915000603")
    # poco = AndroidUiautomationPoco(device)

    auto_setup(__file__, devices=["Android://127.0.0.1:5037/JVR6R18915000603"], logdir=True, compress=90)
    poco = AndroidUiautomationPoco(use_airtest_input=True, screenshot_each_action=False)

    adb = ADB(serialno="JVR6R18915000603")
    recorder = Recorder(adb)

    # 开启录屏
    recorder.start_recording()

    yield poco

    home()
    keyevent("POWER")  # 按电源键锁屏

    # 结束录屏
    recorder.stop_recording(output="test/app/log/test.mp4")

    # 生成报告
    # simple_report(__file__, logpath=True, output="test/app/log.html")

    LogToHtml(
        script_root=__file__,
        log_root="test/app/log",
        lang="zh",
        plugins=["poco.utils.airtest.report"],
    ).report(output_file="test/app/log/log.html")


@pytest.fixture(scope="session", autouse=True)
def run_app(poco: AndroidUiautomationPoco):
    # 唤醒屏幕
    wake()

    # 屏幕解锁
    for num in [1, 2, 5, 8]:
        poco(text=f"{num}").click()

    # 清理某个应用数据
    clear_app("com.huawei.himovie")
    # 启动某个应用
    start_app("com.huawei.himovie")


def test_get_video_title(poco: AndroidUiautomationPoco):
    poco("com.huawei.himovie:id/btn_confirm").click()

    poco("com.huawei.himovie:id/advert_dialog_close_image_view").wait_for_appearance()
    poco("com.huawei.himovie:id/advert_dialog_close_image_view").click()

    poco(text="电影").click()
    poco(text="全部电影").click()

    seen_items = set()
    while True:
        try:
            poco("android.widget.LinearLayout").offspring("com.huawei.himovie:id/title").wait_for_appearance()
            items = poco("android.widget.LinearLayout").offspring("com.huawei.himovie:id/title")
            new_items_found = False

            for item in items:
                title = item.get_text()
                if title not in seen_items:
                    logging.info(title)
                    seen_items.add(title)
                    new_items_found = True

            if not new_items_found:
                break

            # 模拟屏幕向上滑动
            poco.swipe([0.5, 0.8], [0.5, 0.2], duration=1.0)

        except Exception as e:
            logging.error(f"Exception occurred during swipe: {e}")
            break
```

### 特殊开发环境：Win + WSL + airtest/poco
在Win机器上通过WSL进行APP自动化时，使用 `adb devices` 无法获取到设备，此时需要通过以下方式解决（参见：https://jaro.blog/blog/adb-in-wsl2.html）：
1. 确保Win和WSL中使用相同的adb版本，通过 `adb version`查看
2. 在 Win 上执行：
    ```
    adb kill-server
    adb -a -P 5037 nodaemon server
    ```
3. 在 Win 上执行 `ipconfig` 获取IP地址
    ```
    以太网适配器 vEthernet (WSL (Hyper-V firewall)):

    连接特定的 DNS 后缀 . . . . . . . :
    本地链接 IPv6 地址. . . . . . . . : fe80::414:6dac:f1ee:400d%34
    IPv4 地址 . . . . . . . . . . . . : 192.168.144.1
    子网掩码  . . . . . . . . . . . . : 255.255.240.0
    默认网关. . . . . . . . . . . . . :
    ```
4. 在 WSL 中执行 `sudo vim /etc/resolv.conf` 替换 nameserver 为上面获取到的IP（192.168.144.1）
5. 在 WSL 中的 profile （如 ~/.zshrc）配置环境变量：
    ```
    export ADB_SERVER_SOCKET=tcp:192.168.144.1:5037
    ```
6. 在 WSL 中执行`adb devices`即可正常获取设备
7. 调整连接设备的字符串，使用上面的IP代替 127.0.0.1，如下：
    ```
    device = connect_device(f"Android://192.168.144.1:5037/{device_id}")
    poco = AndroidUiautomationPoco(device)
    ```




### 相关引用

- 掘金：https://juejin.cn/user/1275089221067928
- 官网教程：https://airtest.doc.io.netease.com/tutorial/