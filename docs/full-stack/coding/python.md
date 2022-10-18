# python


### 字符编码

因为计算机只能处理数字，如果要处理文本，就必须先把文本转换为数字才能处理。最早的计算机在设计时采用8个比特（bit）作为一个字节（byte），所以，一个字节能表示的最大的整数就是255（二进制11111111=十进制255），如果要表示更大的整数，就必须用更多的字节。比如两个字节可以表示的最大整数是65535，4个字节可以表示的最大整数是4294967295。

- ASCII 编码：大小写英文字母、数字和一些符号
- GB2312 编码: 中文编码
- Unicode 编码：把所有语言（英文/中文/日文/韩文...）都统一到一套编码里
- UTF-8 编码: 本着节约的精神，又出现了把Unicode编码转化为“可变长编码”的UTF-8编码，UTF-8编码把一个Unicode字符根据不同的数字大小编码成1-6个字节，常用的英文字母被编码成1个字节，汉字通常是3个字节，只有很生僻的字符才会被编码成4-6个字节。如果你要传输的文本包含大量英文字符，用UTF-8编码就能节省空间

| 字符 | ASCII | Unicode  | UTF-8    |
| ---- | ----- | -------- | -------- |
| A    |       | 01000001 | 00000000 | 01000001 | 01000001 |
| 中   | x     | 01001110 | 00101101 | 11100100 | 10111000 | 10101101 |


```python
ord('A')   # 65
ord('中')  # 20013
chr(66)    # 'B'
chr(25991) #'文'

print('\u4e2d\u6587') # 中文

# str <=> bytes
'ABC'.encode('ascii')  # b'ABC'
'中文'.encode('utf-8')  # b'\xe4\xb8\xad\xe6\x96\x87'
'中文'.encode('gb2312') # b'\xd6\xd0\xce\xc4'

# 纯英文的str可以用ASCII编码为bytes，内容是一样的，含有中文的str可以用UTF-8编码为bytes。含有中文的str无法用ASCII编码，因为中文编码的范围超过了ASCII编码的范围，Python会报错
'中文'.encode('ascii') # UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-1: ordinal not in range(128)

b'ABC'.decode('ascii')                      #'ABC'
b'\xe4\xb8\xad\xe6\x96\x87'.decode('utf-8') #'中文'

# 如果bytes中只有一小部分无效的字节，可以传入errors='ignore'忽略错误的字节
b'\xe4\xb8\xad\xff'.decode('utf-8', errors='ignore') # 中


# len()函数计算的是str的字符数，如果换成bytes，len()函数就计算字节数

# 计算字符长度
len('ABC') # 3
len('中文') # 2

# 计算字节数
len(b'ABC')                      # 3
len(b'\xe4\xb8\xad\xe6\x96\x87') # 6
len('中文'.encode('utf-8'))      # 6

# 可见，1个中文字符经过UTF-8编码后通常会占用3个字节，而1个英文字符只占用1个字节
```


<br>

### 字符串格式化

```python
'Hello, {0}, 成绩提升了 {1:.1f}%'.format('小明', 17.125)   # 'Hello, 小明, 成绩提升了 17.1%'

"{}://{}".format("http", "www.baidu.com")                 # 'http://www.baidu.com'

"{:0>3d}".format(1)                                       # 补0，001

r = 2.5
s = 3.14 * r ** 2
print(f'The area of a circle with radius {r} is {s:.2f}') # The area of a circle with radius 2.5 is 19.62


data = {"name": "HHB", "age": 18}
payload = """
{{
    "name": "{name}",
    "age": {age},
}}
"""

payload.format(**data)
```

<br>

### 条件判断&循环

```python
# 条件判断
age = 20
if age >= 18:
    print('your age is', age)
    print('adult')


age = 3
if age >= 18:
    print('your age is', age)
    print('adult')
else:
    print('your age is', age)
    print('teenager')


age = 3
if age >= 18:
    print('adult')
elif age >= 6:
    print('teenager')
else:
    print('kid')


# 循环
names = ['Michael', 'Bob', 'Tracy']
for name in names:
    print(name)

sum = 0
n = 99
while n > 0:
    sum = sum + n
    n = n - 2
print(sum)

# break => 退出整个循环
n = 1
while n <= 100:
    if n > 10:
        break
    print(n)
    n = n + 1
print('END')

# continue => 退出当前循环，继续下一轮循环
n = 0
while n < 10:
    n = n + 1
    if n % 2 == 0:
        continue
    print(n)
```

### 闭包

```python
from functools import reduce

def createCounter():
    """闭包"""
    count = 0

    def counter():
        nonlocal count
        count += 1
        return count

    return counter

counterA = createCounter()
print(counterA(), counterA(), counterA(), counterA(), counterA())
```

<br>



### 递归
```python
def recursive_query_file(path: str, pattern: str) -> list:

    result = []

    def recursive(path: str, pattern: str):
        nonlocal result

        path = Path(path)

        result += filter_file(path, pattern)

        sub_folders = [folder for folder in path.iterdir() if folder.is_dir()]

        if sub_folders:
            for folder in sub_folders:
                recursive(folder, pattern)
        else:
            return result

    recursive(path, pattern)

    return result
```

<br>

### functools

- 偏函数

```python
import functools

# 创建偏函数时，实际上可以接收函数对象、*args和**kw这3个参数

print(int("11111", base=2))

# **kw
int2 = functools.partial(int, base=2)  # 实际上固定了int()函数的关键字参数base
print(int2("11111"))

# *args
max2 = functools.partial(max, 10)  # 实际上会把10作为*args的一部分自动加到左边
print(max2(1, 7, 6))
print(max(10, 1, 7, 6))

# functools.partial的作用就是，把一个函数的某些参数给固定住（也就是设置默认值），返回一个新的函数，调用这个新函数会更简单
```

<br>


### collections

- Counter

```python
from collections import Counter

Counter('chenkc') # => Counter({'c': 2, 'h': 1, 'e': 1, 'n': 1, 'k': 1})
```

<br>

### 高阶函数
```python
# map
print(list(map(lambda x: x * x, range(10))))
print(list(map(str, range(10))))

# reduce
print(reduce(lambda x, y: x + y, range(10)))

# filter
print(list(filter(lambda x: x % 2 == 1, range(10))))
print(list(filter(lambda x: str(x) == str(x)[::-1], range(122))))

print(sorted([2, 4, 1, -7, 6]))
print(sorted([2, 4, 1, -7, 6], key=abs))
print(sorted(["a", "A", "b", "X", "y", "M"], key=str.lower))
print(sorted(["a", "A", "b", "X", "y", "M"], key=str.lower, reverse=True))
print(sorted([("Bob", 75), ("Adam", 92), ("Bart", 66), ("Lisa", 88)], key=lambda x: x[0]))
print(sorted([("Bob", 75), ("Adam", 92), ("Bart", 66), ("Lisa", 88)], key=lambda x: x[1], reverse=True))
```

<br>

### 装饰器
```python
import functools

def log(func):
    def wrapper(*args, **kw):
        print("log")
        return func(*args, **kw)

    return wrapper

def log2(text):
    def decorator(func):
        print("log2", text, func.__name__)

        @functools.wraps(func)
        def wrapper(*args, **kw):
            return func(*args, **kw)

        return wrapper

    return decorator

@log
def now():
    print("2015-3-25")

@log
def now1(a):
    print("2015-3-25", a)

@log2("execute")
def now2():
    print("2015-3-25")

now()
now1("param")
now2()
```

<br>

### 日期时间
```python
from datetime import datetime as d

format = "%Y-%m-%dT%H:%M:%S.%fZ"

timestamp = 1528797322
date_time = d.fromtimestamp(timestamp)
print(date_time) # 2018-06-12 04:55:22

date_time = date_time.strftime(format)
print(date_time) # 2018-06-12T04:55:22.000000Z
```

<br>

### 列表(list)

list是一种有序的集合，可以随时添加和删除其中的元素，属于可变对象。

```python
a = ["A","B","C"]
b = ["A","B","C", [1,2,3]]

a[0]
a[-1]
a[::-1]
b[3][1]

len(a)

a + b

a.append(b)

a.insert(0,"1")

a.remove("A")

a[0] = "X"

a.pop(1)
a.pop()

list(range(5)) # [0, 1, 2, 3, 4]

for i,v in enumerate(a):
    print(i,v)
```

<br>

### 元组(tuple)
tuple 是一种特殊的有序列表，一旦初始化就不能修改，它也没有append()，insert()这样的方法，不能根据索引重新赋值。因为tuple不可变，所以代码更安全。

```python
t = ()
t = (1,)
t = (1,2)

len(t)

t[0]
t[0]=1 # TypeError: 'tuple' object does not support item assignment


# “可变的”tuple
t = ('a', 'b', ['A', 'B'])

t[2][0]="X"
t[2][1]="Y"

# t => ('a', 'b', ['X', 'Y'])

# NOTE: 表面上看，tuple的元素确实变了，但其实变的不是tuple的元素，而是list的元素。
# tuple一开始指向的list并没有改成别的list，所以，tuple所谓的“不变”是说，tuple的每个元素，指向永远不变。
# 即指向'a'，就不能改成# 指向'b'，指向一个list，就不能改成指向其他对象，但指向的这个list本身是可变的！
# 理解了“指向不变”后，要创建一个内容也不变的tuple怎么做？那就必须保证tuple的每一个元素本身也不能变。


for i,v in enumerate(t):
    print(i,v)
```

<br>

### 字典(dict)
```python
a = {'device_type': 'cisco_ios', 'username': 'admin', 'password': 'cisco'}
b = {'name': 'r1'}

# 方式一
for k, v in a.items():
    b[k] =  v

# 方式二
b.update(a)

# 方式三
dict(b, **a)

# 方式四
b = {**a, **b}


"name" in b

b["name"]
b.get("name", "")

b.pop("name")


dict(a=1, b=2, c=3) # {'a': 1, 'b': 2, 'c': 3}


for key in a:
    print(key)

for k in a.keys():
    print(k)

for v in a.values():
    print(v)

for k,v in a.items():
    print(k, v)
```
> NOTE:
> 1.dict的key必须是不可变对象
> 2.dict内部存放的顺序和key放入的顺序是没有关系的

<br>

### 集合(set)

set和dict类似，也是一组key的集合，但不存储value。由于key不能重复，所以，在set中，没有重复的key。set可以看成数学意义上的无序和无重复元素的集合。

```python
s = set()
s = set([1, 2, 3])
s = {1, 2, 3}

# 通过add(key)方法可以添加元素到set中，可以重复添加，但不会有效果
s.add(4)
s.add(4)
s.remove(4)


{1, 2, 3, 3, 4, 4} # {1, 2, 3, 4} => 自动去重


s1 = {1, 2, 3}
s2 = {2, 3, 4}

s1 & s2 # 交集, {2, 3}
s1 | s2 # 并集, {1, 2, 3, 4}
s1 - s2 # 差集, {1}
s1 ^ s2 # 补集, {1, 4}


parent = {1, 2, 3, 4}
child = {1, 2}

child.issubset(parent)   # True
parent.issuperset(child) # True
```

<br>

### 不可变对象
```python
# list 为可变对象，经过排序后内容改变
a = ['c', 'b', 'a']
a.sort()
a # ['a', 'b', 'c']


# 字符串为不可变对象，调用相关方法后原始值不变
a = 'abc'
a.replace('a', 'A') # 'Abc'
a                   # 'abc'

# 对于不变对象来说，调用对象自身的任意方法，也不会改变该对象自身的内容。相反，这些方法会创建新的对象并返回，这样，就保证了不可变对象本身永远是不可变的
a = 'abc'
b = a.replace('a', 'A')
b # 'Abc'
a # 'abc'

# 当我们调用a.replace('a', 'A')时，实际上调用方法replace是作用在字符串对象'abc'上的，而这个方法虽然名字叫replace，但却没有改变字符串'abc'的内容。
# 相反，replace方法创建了一个新字符串'Abc'并返回，如果我们用变量b指向该新字符串，就容易理解了，变量a仍指向原有的字符串'abc'，但变量b却指向新字符串'Abc'了
```

<br>

### 函数
```python
help(abs)

abs(10)
max(2, 3, 1, -5)
sum([1,2,3])

int('123')
float('12.34')
str(123)
bool(1)


def emptyfunc():
    pass


# 返回单个值
def my_abs(x):
    if not isinstance(x, (int, float)):
        raise TypeError('bad operand type')
    if x >= 0:
        return x
    else:
        return -x

# 返回多个值(tuple)
import math
def move(x, y, step, angle=0):
    nx = x + step * math.cos(angle)
    ny = y - step * math.sin(angle)
    return nx, ny


# 默认参数
def add(x, y=1):
    return x + y

add(1)
add(1, 3)

# 定义默认参数要牢记一点：默认参数必须指向不变对象！
def add_end(L=[]):
    L.append('END')
    return L

def add_end(L=None):
    if L is None:
        L = []
    L.append('END')
    return L


# 可变参数
def calc(*numbers):
    sum = 0
    for n in numbers:
        sum = sum + n * n
    return sum

# Python允许你在list或tuple前面加一个*号，把list或tuple的元素变成可变参数传进去
calc(*[1, 2])
calc(*(1, 2))

calc(1, 2)
calc()


# 关键字参数
def person(name, age, **kw):
    print('name:', name, 'age:', age, 'other:', kw)

person('Michael', 30)
person('Bob', 35, city='Beijing')
person('Adam', 45, gender='M', job='Engineer')

# **extra表示把extra这个dict的所有key-value用关键字参数传入到函数的**kw参数，kw将获得一个dict，注意kw获得的dict是extra的一份拷贝，对kw的改动不会影响到函数外的extra
extra = {'city': 'Beijing', 'job': 'Engineer'}
person('Jack', 24, **extra)


# 命名关键字参数

# 如果要限制关键字参数的名字，就可以用命名关键字参数，例如，只接收city和job作为关键字参数。这种方式定义的函数如下：
def person(name, age, *, city, job):
    print(name, age, city, job)

person('Jack', 24, city='Beijing', job='Engineer')


# 如果函数定义中已经有了一个可变参数，后面跟着的命名关键字参数就不再需要一个特殊分隔符*了：
def person(name, age, *args, city, job):
    print(name, age, args, city, job)


# 命名关键字参数可以有缺省值，从而简化调用：
def person(name, age, *, city='Beijing', job):
    print(name, age, city, job)

person('Jack', 24, job='Engineer')


# 参数组合
def f1(a, b, c=0, *args, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'args =', args, 'kw =', kw)

def f2(a, b, c=0, *, d, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'd =', d, 'kw =', kw)

f1(1, 2)                    # a = 1 b = 2 c = 0 args = () kw = {}
f1(1, 2, c=3)               # a = 1 b = 2 c = 3 args = () kw = {}
f1(1, 2, 3, 'a', 'b')       # a = 1 b = 2 c = 3 args = ('a', 'b') kw = {}
f1(1, 2, 3, 'a', 'b', x=99) # a = 1 b = 2 c = 3 args = ('a', 'b') kw = {'x': 99}
f2(1, 2, d=99, ext=None)    # a = 1 b = 2 c = 0 d = 99 kw = {'ext': None}


# 最神奇的是通过一个tuple和dict，你也可以调用上述函数：
args = (1, 2, 3, 4)
kw = {'d': 99, 'x': '#'}
f1(*args, **kw) # a = 1 b = 2 c = 3 args = (4,) kw = {'d': 99, 'x': '#'}

args = (1, 2, 3)
kw = {'d': 88, 'x': '#'}
f2(*args, **kw) # a = 1 b = 2 c = 3 d = 88 kw = {'x': '#'}


# 递归函数
使用递归函数的优点是逻辑简单清晰，缺点是过深的调用会导致栈溢出。
# Demo: 计算阶乘 fact(n)=n!=1×2×3×⋅⋅⋅×(n−1)×n=(n−1)!×n=fact(n−1)×n
def fact(n):
    if n==1:
        return 1
    return n * fact(n - 1)

fact(5)


# 尾递归
def fact(n):
    return fact_iter(n, 1)

def fact_iter(num, product):
    if num == 1:
        return product
    return fact_iter(num - 1, num * product)
```
> NOTE:
> 
> 1.对于任意函数，都可以通过类似func(*args, **kw)的形式调用它，无论它的参数是如何定义的
> 
> 2.虽然可以组合多达5种参数，但不要同时使用太多的组合，否则函数接口的可理解性很差
> 
> 3.Python标准的解释器没有针对尾递归做优化，任何递归函数都存在栈溢出的问题


<br>

### 高级特性
```python
# 切片
L = ['Michael', 'Sarah', 'Tracy', 'Bob', 'Jack']

L[0:3] # ['Michael', 'Sarah', 'Tracy']
L[:3]  # ['Michael', 'Sarah', 'Tracy']

L[-2:]   # ['Bob', 'Jack']
L[-2:-1] # ['Bob']

L = list(range(100))
L[:10]   # 前10个数, [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
L[-10:]  # 后10个数，[90, 91, 92, 93, 94, 95, 96, 97, 98, 99]
L[10:20] # 前 11 - 20 个数，[10, 11, 12, 13, 14, 15, 16, 17, 18, 19]
L[:10:2] # 前10个数，每隔两个取一个，[0, 2, 4, 6, 8]
L[::5]   # 所有数，每5个取一个，[0, 5, 10, 15, 20, 25, 30, 35, 40, 45, 50, 55, 60, 65, 70, 75, 80, 85, 90, 95]
L[::-1]  # 逆序输出列表所有数
L[:]     # 所有数

# 元组切片
(0, 1, 2, 3, 4, 5)[:3] # (0, 1, 2)

# 字符串切片
'ABCDEFG'[:3]          # 'ABC'

# 使用切片完成 trim 函数（去除首尾空格）
def trim(s):
    while len(s) >= 1 and s[0] == ' ':
        s = s[1:]
    while len(s) >= 1 and s[-1:] == ' ':
        s = s[:-1]
    return s
```

<br>

### 列表推导式
```python
data = list(range(1, 11))

[x * x for x in range(1, 11)]

[x * x for x in range(1, 11) if x % 2 == 0]

[x if x % 2 == 0 else -x for x in range(1, 11)]

[m + n for m in 'ABC' for n in 'XYZ']
```

<br>

### 小技巧
```python
# 判断是否为数字，可以用于int()转换场景中
"123".isdigit()


# 带T的日期格式
format = "%Y-%m-%dT%H:%M:%S.%fZ" # 2018-06-12T04:55:22.000000Z
```

<br>

### 相关拓展
- [Python语言规范](https://zh-google-styleguide.readthedocs.io/en/latest/google-python-styleguide/python_language_rules/)
- [字符串和编码](https://www.liaoxuefeng.com/wiki/1016959663602400/1017075323632896)