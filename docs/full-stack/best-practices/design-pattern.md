# 设计模式


### 简单工厂模式

```python
from abc import ABCMeta, abstractmethod


class Payment(metaclass=ABCMeta):
    @abstractmethod
    def pay(self, money):
        pass


class AliPay(Payment):
    def pay(self, money):
        print(f"支付宝支付 {money} 元")


class WechatPay(Payment):
    def pay(self, money):
        print(f"微信支付 {money} 元")


class PaymentFactory:
    def create_payment(self, method):
        if method == "AliPay":
            return AliPay()
        elif method == "wechat":
            return WechatPay()
        else:
            raise TypeError(f"No such payment named {method}")


factory = PaymentFactory()
payment = factory.create_payment("AliPay")
payment.pay(1000)
```

### 工厂方法模式

```python
from abc import ABCMeta, abstractmethod


class Payment(metaclass=ABCMeta):
    @abstractmethod
    def pay(self, money):
        pass


class AliPay(Payment):
    def pay(self, money):
        print(f"支付宝支付 {money} 元")


class WechatPay(Payment):
    def pay(self, money):
        print(f"微信支付 {money} 元")


class PaymentFactory(metaclass=ABCMeta):
    @abstractmethod
    def create_payment(self):
        pass


class AliPayFactory(PaymentFactory):
    def create_payment(self):
        return AliPay()


class WechatFactory(PaymentFactory):
    def create_payment(self):
        return WechatPay()


factory = AliPayFactory()
pay = factory.create_payment()
pay.pay(1000)


factory = WechatFactory()
pay = factory.create_payment()
pay.pay(1000)
```

### 抽象工厂模式

```python
from abc import ABCMeta, abstractmethod


# ======= 抽象产品 =======
class PhoneShell(metaclass=ABCMeta):
    @abstractmethod
    def show_shell(self):
        pass


class CPU(metaclass=ABCMeta):
    @abstractmethod
    def show_cpu(self):
        pass


class OS(metaclass=ABCMeta):
    @abstractmethod
    def show_os(self):
        pass


# ======= 抽象工厂 =======
class PhoneFactory(metaclass=ABCMeta):
    @abstractmethod
    def make_shell(self):
        pass

    @abstractmethod
    def make_cpu(self):
        pass

    @abstractmethod
    def make_os(self):
        pass


# ======= 具体产品 =======
class SmallShell(PhoneShell):
    def show_shell(self):
        print("普通手机小手机壳")


class BigShell(PhoneShell):
    def show_shell(self):
        print("普通手机大手机壳")


class AppleShell(PhoneShell):
    def show_shell(self):
        print("苹果手机壳")


class SnapDragonCPU(CPU):
    def show_cpu(self):
        print("骁龙CPU")


class MediaTekCPU(CPU):
    def show_cpu(self):
        print("联发科CPU")


class AppleCPU(CPU):
    def show_cpu(self):
        print("苹果CPU")


class AndroidOS(OS):
    def show_os(self):
        print("安卓OS")


class AppleOS(OS):
    def show_os(self):
        print("苹果OS")


# ======= 具体工厂 =======
class MiFactory(PhoneFactory):
    def make_shell(self):
        return BigShell()

    def make_cpu(self):
        return SnapDragonCPU()

    def make_os(self):
        return AndroidOS()


# 限制组件搭配，不允许随意组装
class iPhoneFactory(PhoneFactory):
    def make_shell(self):
        return AppleShell()

    def make_cpu(self):
        return AppleCPU()

    def make_os(self):
        return AppleOS()


# ======= 客户端 =======
class Phone:
    def __init__(self, shell: PhoneShell, cpu: CPU, os: OS):
        self.shell = shell
        self.cpu = cpu
        self.os = os

    def show_info(self):
        print("手机信息：")
        self.shell.show_shell()
        self.cpu.show_cpu()
        self.os.show_os()


def make_phone(factory: PhoneFactory):
    shell = factory.make_shell()
    cpu = factory.make_cpu()
    os = factory.make_os()

    return Phone(shell, cpu, os)


mi_phone = make_phone(MiFactory())
mi_phone.show_info()

iphone = make_phone(iPhoneFactory())
iphone.show_info()
```


### 建造者模式

```python
from abc import ABCMeta, abstractmethod


class Player:
    def __init__(self, face=None, body=None, arm=None, leg=None):
        self.face = face
        self.body = body
        self.arm = arm
        self.leg = leg

    def __str__(self):
        return f"{self.face} {self.body} {self.arm} {self.leg}"


class PlayerBuilder(metaclass=ABCMeta):
    @abstractmethod
    def build_face(self):
        pass

    @abstractmethod
    def build_body(self):
        pass

    @abstractmethod
    def build_arm(self):
        pass

    @abstractmethod
    def build_leg(self):
        pass


class SexyGirlBuilder(PlayerBuilder):
    def __init__(self):
        self.player = Player()

    def build_face(self):
        self.player.face = "漂亮脸蛋"

    def build_body(self):
        self.player.body = "苗条"

    def build_arm(self):
        self.player.arm = "修长手臂"

    def build_leg(self):
        self.player.leg = "大长腿"


class MonsterBuilder(PlayerBuilder):
    def __init__(self):
        self.player = Player()

    def build_face(self):
        self.player.face = "怪兽脸"

    def build_body(self):
        self.player.body = "怪兽身材"

    def build_arm(self):
        self.player.arm = "长毛的胳膊"

    def build_leg(self):
        self.player.leg = "长毛的腿"


class PlayerDirector:
    # 控制游戏角色组装顺序（比如先要有躯干，才能设置其他四肢）
    def build_player(self, builder):
        builder.build_body()
        builder.build_face()
        builder.build_arm()
        builder.build_leg()

        return builder.player


sexy_girl = SexyGirlBuilder()
director = PlayerDirector()
persion = director.build_player(sexy_girl)
print(persion)


monster = MonsterBuilder()
director = PlayerDirector()
persion = director.build_player(monster)
print(persion)
```

### 单例模式

```python
class Singleton:
    def __new__(cls, *args, **kwargs):
        if not hasattr(cls, "_instance"):
            cls._instance = super(Singleton, cls).__new__(cls)

        return cls._instance


class MyClass(Singleton):
    def __init__(self, a):
        self.a = a


a = MyClass(10)
b = MyClass(20)

print(a.a)  # 20
print(b.a)  # 20
print(id(a), id(b))  # id(a) = id(b)
```

### 适配器模式

```python
from abc import ABCMeta, abstractmethod


class Payment(metaclass=ABCMeta):
    @abstractmethod
    def pay(self, money):
        pass


class AliPay(Payment):
    def pay(self, money):
        print(f"支付宝支付 {money} 元")


class WechatPay(Payment):
    def pay(self, money):
        print(f"微信支付 {money} 元")


# Note: 银联支付用的是 cost 其他支付用的是 pay, 需要用到下面的适配器模式进行转换
class BankPay:
    def cost(self, money):
        print(f"银联支付 {money} 元")


# 类适配器
class NewBankPay(Payment, BankPay):
    def pay(self, money):
        self.cost(money)


# 对象适配器
class PaymentAdapter(Payment):
    def __init__(self, payment):
        self.payment = payment

    def pay(self, money):
        return self.payment.cost(money)


pay = AliPay()
pay.pay(1000)

pay = NewBankPay()
pay.pay(1000)

pay = PaymentAdapter(NewBankPay())
pay.pay(1000)
```

### 桥模式

```python
from abc import ABCMeta, abstractmethod


class Shape(metaclass=ABCMeta):
    # 把颜色和形状做关联，将颜色作为形状的一个属性
    def __init__(self, color):
        self.color = color

    @abstractmethod
    def draw(self):
        pass


class Color(metaclass=ABCMeta):
    @abstractmethod
    def paint(self, shape):
        pass


class Rectangle(Shape):
    name = "长方形"

    def draw(self):
        # draw rectangle logic
        self.color.paint(self)


class Circle(Shape):
    name = "圆形"

    def draw(self):
        # draw circle logic
        self.color.paint(self)


class Red(Color):
    def paint(self, shape):
        print(f"红色的{shape.name}")


class Blue(Color):
    def paint(self, shape):
        print(f"蓝色的{shape.name}")


shape = Rectangle(Red())
shape.draw()

shape = Rectangle(Blue())
shape.draw()

shape = Circle(Red())
shape.draw()
```

### 组合模式

```python
from abc import ABCMeta, abstractmethod


# 抽象组件
class Graphic(metaclass=ABCMeta):
    @abstractmethod
    def draw(self):
        pass


# 叶子组件
class Point(Graphic):
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __str__(self):
        return f"点 ({self.x}, {self.y})"

    def draw(self):
        print(str(self))


# 叶子组件
class Line(Graphic):
    def __init__(self, p1, p2):
        self.p1 = p1
        self.p2 = p2

    def __str__(self):
        return f"线段 ({self.p1}, {self.p2})"

    def draw(self):
        print(str(self))


# 复合组件（包含多个叶子组件）
class Picture(Graphic):
    def __init__(self, iterable):
        self.children = []

        for g in iterable:
            self.add(g)

    def add(self, graphic):
        self.children.append(graphic)

    def draw(self):
        print("----复合图形----")
        for g in self.children:
            g.draw()
        print("----复合图形----")


line = Line(Point(1, 1), Point(2, 2))
print(line)


point1 = Point(2, 3)
line1 = Line(Point(3, 4), Point(6, 7))
line2 = Line(Point(1, 5), Point(2, 8))
pic1 = Picture([point1, line1, line2])

point2 = Point(4, 4)
line3 = Line(Point(1, 1), Point(0, 0))
pic2 = Picture([point2, line3])

pic = Picture([pic1, pic2])
pic.draw()
```

### 外观模式

```python
# 子系统类
class CPU:
    def run(self):
        print("CPU开始运行")

    def stop(self):
        print("CPU停止运行")


# 子系统类
class Disk:
    def run(self):
        print("硬盘开始工作")

    def stop(self):
        print("硬盘停止工作")


# 子系统类
class Memory:
    def run(self):
        print("内存开始运行")

    def stop(self):
        print("内存停止运行")


# 外观类
class Computer:
    def __init__(self):
        self.cpu = CPU()
        self.disk = Disk()
        self.memory = Memory()

    def run(self):
        self.cpu.run()
        self.disk.run()
        self.memory.run()

    def stop(self):
        self.cpu.stop()
        self.disk.stop()
        self.memory.stop()


computer = Computer()
computer.run()
computer.stop()
```

### 代理模式

```python
from abc import ABCMeta, abstractmethod


# 抽象实体
class Subject(metaclass=ABCMeta):
    @abstractmethod
    def get_content(self):
        pass

    @abstractmethod
    def set_content(self, content):
        pass


# 实体
class RealSubject(Subject):
    def __init__(self, filename):
        self.filename = filename

        print("读取文件内容")
        with open(filename, "r") as f:
            self.content = f.read()

    def get_content(self):
        return self.content

    def set_content(self, content):
        with open(self.filename, "w") as f:
            f.write(content)


# 虚代理
class VirtualProxy(Subject):
    def __init__(self, filename):
        self.filename = filename
        self.subject = None  # 初始化时设置为空，仅当调用 get_content 时才去调用真实的 get_content 方法

    def get_content(self):
        if not self.subject:
            self.subject = RealSubject(self.filename)
            return self.subject.get_content()

    def set_content(self, content):
        if not self.subject:
            self.subject = RealSubject(self.filename)

        return self.subject.set_content(content)


# 保护代理
class ProtectedProxy(Subject):
    def __init__(self, filename):
        self.subject = RealSubject(filename)

    def get_content(self):
        return self.subject.get_content()

    def set_content(self, content):
        # 验证权限相关逻辑 ...
        raise PermissionError("无写入权限")


# NOTE: 在使用虚代理前，构造RealSubject时（此时还未调用get_content()）已经加载具体的文件了
subject = RealSubject("/mnt/d/src/python/TestBase/tmp/test.txt")
subject.get_content()

# NOTE: 在使用虚代理后，构造RealSubject时不会加载具体的文件，仅在调用 get_content 时才加哉文件
subject = VirtualProxy("/mnt/d/src/python/TestBase/tmp/test.txt")
print(subject.get_content())


# NOTE: 在使用保护代理后，仅有读权限，无写权限
subject = ProtectedProxy("/mnt/d/src/python/TestBase/tmp/test.txt")
print(subject.get_content())
subject.set_content("abc")  # PermissionError: 无写入权限
```

### 责任链模式

```python
from abc import ABCMeta, abstractmethod


# 抽象处理者
class Handler(metaclass=ABCMeta):
    @abstractmethod
    def handle_leave(self, day):
        pass


# 具体处理者
class GeneralManager(Handler):
    def handle_leave(self, day):
        if day < 10:
            print(f"总经理准假 {day} 天")
        else:
            print("不符合公司规定, 不予批准")


# 具体处理者
class ProjectManager(Handler):
    def __init__(self):
        self.next = GeneralManager()

    def handle_leave(self, day):
        if day <= 5:
            print(f"项目经理准假 {day} 天")
        else:
            print("项目经理权限不足")
            self.next.handle_leave(day)


# 具体处理者
class TeamLeader(Handler):
    def __init__(self):
        self.next = ProjectManager()

    def handle_leave(self, day):
        if day <= 3:
            print(f"总经理准假 {day} 天")
        else:
            print("Team Leader 权限不足")
            self.next.handle_leave(day)


# 责任链：Team Leader -> Project Manager -> General Manager

handle = TeamLeader()
handle.handle_leave(1)
handle.handle_leave(5)
handle.handle_leave(7)
handle.handle_leave(11)
```

### 观察者模式

```python
from abc import ABCMeta, abstractmethod

# 抽象订阅者
class Observer(metaclass=ABCMeta):
    @abstractmethod
    def update(self, publisher):  # publisher 是一个 Publisher 类的对象
        pass


# 抽象发布者
class Publisher:
    def __init__(self):
        self.observers = []

    def attach(self, observer):
        self.observers.append(observer)

    def detach(self, observer):
        self.observers.remove(observer)

    def notify(self):
        for observer in self.observers:
            observer.update(self)


# 具体发布者
class StaffPublisher(Publisher):
    def __init__(self, company_info=None):
        super().__init__()
        self.__company_info = company_info

    @property
    def company_info(self):
        return self.__company_info

    @company_info.setter
    def company_info(self, info):
        self.__company_info = info
        self.notify()


class Staff(Observer):
    def __init__(self):
        self.company_info = None

    def update(self, publisher):
        self.company_info = publisher.company_info


publisher = StaffPublisher("初始化公司信息")
staff01 = Staff()
staff02 = Staff()

publisher.attach(staff01)
publisher.attach(staff02)

publisher.company_info = "公司今年业绩非常好，给大家发奖金"
print(staff01.company_info)
print(staff02.company_info)


publisher.detach(staff01)
publisher.company_info = "公司明天放假"
print(staff01.company_info)
print(staff02.company_info)
```

### 策略模式

```python
from abc import ABCMeta, abstractmethod


class Strategy(metaclass=ABCMeta):
    @abstractmethod
    def execute(self, data):
        pass


class FastStrategy(Strategy):
    def execute(self, data):
        print(f"用较快的策略处理 {data}")


class SlowStrategy(Strategy):
    def execute(self, data):
        print(f"用较慢的策略处理 {data}")


class Context:
    def __init__(self, strategy, data):
        self.data = data
        self.strategy = strategy

    def set_strategy(self, strategy):
        self.strategy = strategy

    def do_strategy(self):
        self.strategy.execute(self.data)


data = "[...]"
s1 = FastStrategy()
s2 = SlowStrategy()

context = Context(s1, data)
context.do_strategy()


context = Context(s2, data)
context.do_strategy()
```

### 模板方法模式

```python
from time import sleep
from abc import ABCMeta, abstractmethod


class Window(metaclass=ABCMeta):
    @abstractmethod
    def start(self):
        pass

    @abstractmethod
    def repaint(self):
        pass

    @abstractmethod
    def stop(self):
        pass

    # 模板方法
    def run(self):
        self.start()

        while True:
            try:
                self.repaint()
                sleep(1)
            except KeyboardInterrupt:
                break
        self.stop()


class MyWindow(Window):
    def __init__(self, msg):
        self.msg = msg

    def start(self):
        print("窗口开始运行")

    def stop(self):
        print("窗口结束运行")

    def repaint(self):
        print(self.msg)


window = MyWindow("hello...")
window.run()
```

<br><br>


### 学习资源
- [Python之常用设计模式](https://www.bilibili.com/video/BV19541167cn)