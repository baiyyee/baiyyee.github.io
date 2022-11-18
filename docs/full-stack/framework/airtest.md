# Airtest


### 安装
下载：https://airtest.netease.com/

<br>

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

<br>

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


### 常见问题
- [Android连接常见问题](https://airtest.doc.io.netease.com/IDEdocs/device_connection/2_android_faq/)
- [Android手机助手](https://mp.weixin.qq.com/s/EH0aQnr2AwG0MmFdgoE7mw)
- [常用模拟器连接](https://mp.weixin.qq.com/s/us4Jr9t21nNyKs-bGD9yyg)