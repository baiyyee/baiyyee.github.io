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

- namedtuple

    ```python
    from collections import namedtuple
    Point = namedtuple('Point', ['x', 'y'])
    p = Point(1, 2)
    p.x # 1
    p.y # 2
    ```

- deque

    deque是为了高效实现插入和删除操作的双向列表，适合用于队列和栈

    ```python
    from collections import deque
    q = deque(['a', 'b', 'c'])
    q.append('x')
    q.appendleft('y')
    q # deque(['y', 'a', 'b', 'c', 'x'])
    ```

- defaultdict

    使用dict时，如果引用的Key不存在，就会抛出KeyError。如果希望key不存在时，返回一个默认值，就可以用defaultdict
    
    ```python
    from collections import defaultdict
    dd = defaultdict(lambda: 'N/A')
    dd['key1'] = 'abc'
    dd['key1'] # key1存在, 'abc'
    dd['key2'] # key2不存在，返回默认值 'N/A'
    ```

- OrderedDict

    OrderedDict的Key会按照插入的顺序排列，不是Key本身排序

    ```python
    from collections import OrderedDict
    d = dict([('a', 1), ('b', 2), ('c', 3)])
    d # dict的Key是无序的, {'a': 1, 'c': 3, 'b': 2}
    od = OrderedDict([('a', 1), ('b', 2), ('c', 3)])
    od # OrderedDict的Key是有序的, OrderedDict([('a', 1), ('b', 2), ('c', 3)])
    ```

- ChainMap

    ChainMap可以把一组dict串起来并组成一个逻辑上的dict。ChainMap本身也是一个dict，但是查找的时候，会按照顺序在内部的dict依次查找。

    ```python
    from collections import ChainMap

    cmd = {"a": "cmd"}
    env = {"a": "env"}
    default = {"a": "default"}

    param = ChainMap(cmd, env, default)
    print(param["a"]) # cmd

    param = ChainMap(env, cmd, default)
    print(param["a"]) # env
    ```

    > NOTE:
    > 
    > ChainMap 可用于按特定优先级取参

<br>

### itertools
```python
import itertools


natuals = itertools.count(1)
for n in natuals:
    print(n)


cs = itertools.cycle('ABC') # 注意字符串也是序列的一种
for c in cs:
    print(c)


ns = itertools.repeat('A', 3)
for n in ns:
    print(n)


natuals = itertools.count(1)
ns = itertools.takewhile(lambda x: x <= 10, natuals)
list(ns) # [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]


for c in itertools.chain('ABC', 'XYZ'):
     print(c) # 迭代效果：'A' 'B' 'C' 'X' 'Y' 'Z'


for key, group in itertools.groupby('AAABBBCCAAA'):
    print(key, list(group))

# A ['A', 'A', 'A']
# B ['B', 'B', 'B']
# C ['C', 'C']
# A ['A', 'A', 'A']

or key, group in itertools.groupby('AaaBBbcCAAa', lambda c: c.upper()):
    print(key, list(group))

# A ['A', 'a', 'a']
# B ['B', 'B', 'b']
# C ['c', 'C']
# A ['A', 'A', 'a']


# 计算圆周率

def pi(n):
    odds = itertools.takewhile(lambda x: x < 2 * n, itertools.count(1, 2))    
    fac = itertools.cycle([1, -1])

    sum = 0   
    for i in odds:
        sum += 4 * next(fac) / i
    return sum
```

<br>

### 高阶函数
```python
# map
print(list(map(lambda x: x * x, range(10))))
print(list(map(str, range(10))))

# reduce
from functools import reduce
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

### 匿名函数
```python
def f(x):
    return x * x

lambda x: x * x

list(map(lambda x: x * x, [1, 2, 3, 4, 5, 6, 7, 8, 9]))

def build(x, y):
    return lambda: x * x + y * y
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
# Demo: 计算阶乘 fact(n)=n!=1×2×3×⋅⋅⋅×(n−1)×n=(n−1)!×n=fact(n−1)×n
def fact(n):
    if n==1:
        return 1
    return n * fact(n - 1)

fact(5)

# Demo: 递归查询文件

def filter_file(path: str, pattern: str) -> list:
    return sorted([str(file) for file in Path(path).glob(pattern)])

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

- 切片

    ```python
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

- 迭代

    如果给定一个list或tuple，我们可以通过for循环来遍历这个list或tuple，这种遍历我们称为迭代（Iteration）。

    ```python
    for i in range(10):
        print(i)

    for char in "ABCD":
        print(char)

    for i, value in enumerate(['A', 'B', 'C']):
        print(i, value)
    
    from collections.abc import Iterable
    isinstance("ABCD", Iterable)    # True
    isinstance([1,2,3,4], Iterable) # True
    isinstance(123, Iterable)       # False
    ```

- 生成器(generator)

    在Python中，这种一边循环一边计算的机制，称为生成器：generator。要创建一个generator，有很多种方法。第一种方法很简单，只要把一个列表生成式的[]改成()，就创建了一个generator

    ```python
    L = [x * x for x in range(10)] # 列表
    g = (x * x for x in range(10)) # 生成器

    for v in g:
        print(v)


    # 斐波拉契数列(Fibonacci)
    def fib(data):
        n, a, b = 0, 0, 1
        
        while n < data:
            yield b
            a, b = b, a + b
            n = n + 1

    # 如果一个函数定义中包含yield关键字，那么这个函数就不再是一个普通函数，而是一个generator函数，调用一个generator函数将返回一个generator

    # 杨辉三角
              1
             / \
            1   1
           / \ / \
          1   2   1
         / \ / \ / \
        1   3   3   1
       / \ / \ / \ / \
      1   4   6   4   1
     / \ / \ / \ / \ / \
    1   5   10  10  5   1

    def triangles(data):
        n = 0
        L = [1]

        while n < data:
            yield L
            L = [1] + [L[n] + L[n+1] for n in range(len(L) - 1)] + [1]
            n = n + 1
    ```

- 迭代器

    可以被next()函数调用并不断返回下一个值的对象称为迭代器：Iterator

    ```python
    from collections.abc import Iterator

    isinstance((x for x in range(10)), Iterator) # True
    isinstance([], Iterator)                     # False
    isinstance({}, Iterator)                     # False
    isinstance('abc', Iterator)                  # False

    # 生成器都是Iterator对象，但list、dict、str虽然是Iterable，却不是Iterator
    # 把list、dict、str等Iterable变成Iterator可以使用iter()函数
    isinstance(iter([]), Iterator)    # True
    isinstance(iter('abc'), Iterator) # True
    ```
    > NOTE:
    >
    > Iterator甚至可以表示一个无限大的数据流，例如全体自然数。而使用list是永远不可能存储全体自然数的

<br>

### 列表推导式
```python
data = list(range(1, 11))

[x * x for x in range(1, 11)]

[x * x for x in range(1, 11) if x % 2 == 0]

[x if x % 2 == 0 else -x for x in range(1, 11)]

[m + n for m in 'ABC' for n in 'XYZ']

import os
[d for d in os.listdir('.')]
```

<br>

### 面向对象编程

- 类和实例
    ```python
    class Student(object):

        def __init__(self, name, score):
            self.name = name
            self.score = score


    bart = Student('Bart Simpson', 59)
    ```

- 继承和多态
    ```python
    class Animal(object):
        def run(self):
            print('Animal is running...')

    class Dog(Animal):

        def run(self):
            print('Dog is running...')

    class Cat(Animal):

        def run(self):
            print('Cat is running...')


    # 新增一个Animal的子类，不必对run_twice()做任何修改，实际上，任何依赖Animal作为参数的函数或者方法都可以不加修改地正常运行，原因就在于多态
    def run(animal):
        animal.run()

    run(Animal()) # Animal is running...
    run(Dog())    # Dog is running...
    run(Cat())    # Cat is running...
    ```

- 实例属性和类属性
    ```python
    class Student(object):
        country = "China"

        def __init__(self, name):
            self.name = name
    ```
    
    > NOTE:
    >
    > 在编写程序的时候，千万不要对实例属性和类属性使用相同的名字，因为相同名称的实例属性将屏蔽掉类属性，但是当你删除实例属性后，再使用相同的名称，访问到的将是类属性

- 多重继承(MixIn)

    MixIn的目的就是给一个类增加多个功能，这样，在设计类的时候，我们优先考虑通过多重继承来组合多个MixIn的功能，而不是设计多层次的复杂的继承关系。

    ```python
    class Animal(object):
        pass

    # 大类:
    class Mammal(Animal):
        pass

    class Bird(Animal):
        pass

    # 各种动物:
    class Dog(Mammal):
        pass

    class Bat(Mammal):
        pass

    class Parrot(Bird):
        pass

    class Ostrich(Bird):
        pass


    class RunnableMixIn(object):
        def run(self):
            print('Running...')

    class FlyableMixIn(object):
        def fly(self):
            print('Flying...')


    class Dog(Mammal, RunnableMixIn, CarnivorousMixIn):
        pass
    ```

- 枚举类(Enum)

    Enum可以把一组相关常量定义在一个class中，且class不可变，而且成员可以直接比较。

    ```python
    from enum import Enum, unique

    class Gender(Enum):
        Male = 0
        Female = 1
        
    # @unique装饰器可以帮助我们检查保证没有重复值
    @unique
    class Weekday(Enum):
        Sun = 0
        Mon = 1
        Tue = 2
        Wed = 3
        Thu = 4
        Fri = 5
        Sat = 6


    print(Weekday.Tue)       # Weekday.Tue
    print(Weekday['Tue'])    # Weekday.Tue
    print(Weekday.Tue.name)  # Tue
    print(Weekday.Tue.value) # 2
    print(Weekday(1))        # Weekday.Mon


    for data in Weekday:
        print(data.name, data.value)
    ```

- 异常处理
    ```python
    try:
        print('try...')
        r = 10 / int('2')
        print('result:', r)
    except ValueError as e:
        print('ValueError:', e)
    except ZeroDivisionError as e:
        print('ZeroDivisionError:', e)
    else:
        print('no error!')
    finally:
        print('finally...')
    print('END')


    # 自定义异常和抛出异常
    class FooError(ValueError):
        pass

    def foo(s):
        n = int(s)
        if n==0:
            raise FooError('invalid value: %s' % s)
        return 10 / n

    foo('0')
    ```

- __slots__
    ```python
    class Student(object):
        __slots__ = ('name', 'age')
    ```

    > NOTE:
    >
    > Python允许在定义class的时候，定义一个特殊的__slots__变量，来限制该class实例能添加的属性

- @property

    ```python
    class Student(object):

        @property
        def birth(self):
            return self._birth

        @birth.setter
        def birth(self, value):
            self._birth = value

        @property
        def age(self):
            return 2015 - self._birth
    ```
    > NOTE:
    >
    > 只定义getter方法，不定义setter方法就是一个只读属性

<br>

### IO 编程

- 文件读写


    | 模式 | 描述                                                                                                                                                             |
    | ---- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
    | t    | 文本模式 (默认)                                                                                                                                                  |
    | x    | 写模式，新建一个文件，如果该文件已存在则会报错                                                                                                                   |
    | b    | 二进制模式                                                                                                                                                       |
    | +    | 打开一个文件进行更新(可读可写)                                                                                                                                   |
    | U    | 通用换行模式（不推荐）                                                                                                                                           |
    | r    | 以只读方式打开文件。文件的指针将会放在文件的开头。这是默认模式                                                                                                   |
    | rb   | 以二进制格式打开一个文件用于只读。文件指针将会放在文件的开头。这是默认模式。一般用于非文本文件如图片等                                                           |
    | r+   | 打开一个文件用于读写。文件指针将会放在文件的开头                                                                                                                 |
    | rb+  | 以二进制格式打开一个文件用于读写。文件指针将会放在文件的开头。一般用于非文本文件如图片等                                                                         |
    | w    | 打开一个文件只用于写入。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件                                           |
    | wb   | 以二进制格式打开一个文件只用于写入。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。一般用于非文本文件如图片等   |
    | w+   | 打开一个文件用于读写。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件                                             |
    | wb+  | 以二进制格式打开一个文件用于读写。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。一般用于非文本文件如图片等     |
    | a    | 打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾。也就是说，新的内容将会被写入到已有内容之后。如果该文件不存在，创建新文件进行写入             |
    | ab   | 以二进制格式打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾。也就是说，新的内容将会被写入到已有内容之后。如果该文件不存在，创建新文件进行写入 |
    | a+   | 打开一个文件用于读写。如果该文件已存在，文件指针将会放在文件的结尾。文件打开时会是追加模式。如果该文件不存在，创建新文件用于读写                                 |
    | ab+  | 以二进制格式打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾。如果该文件不存在，创建新文件用于读写                                             |

    ```python
    with open("./pytest.log", "r") as f:
        for line in f:
            print(line)

    with open("./pytest.log", "r") as f:
        print(f.read())

    with open("./pytest.log", "r") as f:
        while size := f.read(10):
            print(size)

    with open("./pytest.log", "r") as f:
        lines = f.readlines()
        for line in lines:
            print(line)

    
    with open("./pytest.log", "r") as f:
        line = f.readline()
        while line:
            print(line)
            line = f.readline()

    # 海象运算符
    with open("./pytest.log", "r") as f:
        while line := f.readline():
            print(line)
    ```

- StringIO

    ```python
    import pandas
    from io import StringIO
    from PIL import Image, ImageDraw

    log = """
    1639362912.431	127.0.0.1	expires=Wed, 13-Dec-23 02:35:12 GMT; domain=127.0.0.1; path=/;
    1639362912.520	127.0.0.1	expires=Wed, 13-Dec-23 02:35:12 GMT; domain=127.0.0.1; path=/;
    1639362912.580	127.0.0.1	expires=Wed, 13-Dec-23 02:35:12 GMT; domain=127.0.0.1; path=/;
    """

    df = pandas.read_csv(StringIO(log))


    f = StringIO()
    f.write('hello')    # 5
    f.write(' ')        # 1
    f.write('world!')   # 6
    print(f.getvalue()) # hello world!


    f = StringIO('Hello!\nHi!\nGoodbye!')
    while True:
        s = f.readline()
        if s == '':
            break
        print(s.strip())
    ```

- BytesIO

    ```python
    import requests
    from io import BytesIO
    from PIL import Image, ImageDraw


    image = requests.get("https://www.baidu.com/img/PCtm_d9c8750bed0b3c7d089fa7d55720d6cf.png").content
    image = BytesIO(image)

    img = Image.open(image)
    draw = ImageDraw.Draw(img)
    draw.text((28, 26), "Baidu", fill=(0, 0, 0))
    image_new = BytesIO()
    img.save(image_new, "png")


    f = BytesIO()
    f.write('中文'.encode('utf-8')) # 6
    print(f.getvalue())             # b'\xe4\xb8\xad\xe6\x96\x87'

    f = BytesIO(b'\xe4\xb8\xad\xe6\x96\x87')
    f.read() # b'\xe4\xb8\xad\xe6\x96\x87'
    ```

    > NOTE:
    > 
    > StringIO和BytesIO是在内存中操作str和bytes的方法，使得和读写文件具有一致的接口。

- 序列化

    我们把变量从内存中变成可存储或传输的过程称之为序列化，在Python中叫pickling，反过来，把变量内容从序列化的对象重新读到内存里称之为反序列化，即unpickling。

    ```python
    import pickle
    d = dict(name='Bob', age=20, score=88)
    pickle.dumps(d)

    f = open('dump.txt', 'wb')
    pickle.dump(d, f)
    f.close()


    f = open('dump.txt', 'rb')
    d = pickle.load(f)
    f.close()
    d # {'age': 20, 'score': 88, 'name': 'Bob'}



    # json 序列化
    import json
    resource = {"now": date.get_time(), "user_id": 0, "user_email": ""}
    json.dumps(resource, indent=4, ensure_ascii=False)
    with open(path, "w") as f:
        json.dump(resource, f, indent=4, ensure_ascii=False)

    # json 反序列化
    with open(path, "r") as f:
        resource = json.load(f)
    ```

<br>

### 进程和线程

- 子进程

    ```python
    import subprocess

    print('$ nslookup www.python.org')
    r = subprocess.call(['nslookup', 'www.python.org'])


    print('$ nslookup')
    p = subprocess.Popen(['nslookup'], stdin=subprocess.PIPE, stdout=subprocess.PIPE, stderr=subprocess.PIPE)
    output, err = p.communicate(b'set q=mx\npython.org\nexit\n')
    print(output.decode('utf-8'))
    print('Exit code:', p.returncode)
    ```

- 多进程

    ```python
    import time
    import random
    from multiprocessing import Process


    def func(name):
        time.sleep(random.randrange(1,5))
        print(f"我是{name}子进程")
        
    process_list = []
    for i in range(3):
        p = Process(target=func, args=(f"son{i+1}",))
        p.start()
        process_list.append(p)
        
    for i in process_list:
        i.join()

    print("主进程结束！")
    ```

- 进程间通信

    ```python
    # Queue
    from multiprocessing import Process, Queue


    def func(q):
        q.put([1,2,3])
        
    Q = Queue(5)
    for i in range(2):
        process = Process(target=func, args=(Q,))
        process.start()
        process.join()
        
    print(Q.qsize())
    print(Q.get()) 
    print(Q.get())
    print(Q.qsize())


    # Managers
    from multiprocessing import Process, Manager


    def func(lst, dct):
        for i in range(3):
            lst.append(i)
            
        dct["name"] = "my"
        
    with Manager() as manager:
        L = manager.list()
        D = manager.dict()
        
        process = Process(target=func, args=(L, D))
        process.start()
        process.join()
        
        print(L)
        print(D)

    ```

- ThreadLocal

    一个ThreadLocal变量虽然是全局变量，但每个线程都只能读写自己线程的独立副本，互不干扰。ThreadLocal解决了参数在一个线程中各个函数之间互相传递的问题。


    ```python
    import threading
        
    # 创建全局ThreadLocal对象:
    local_school = threading.local()

    def process_student():
        # 获取当前线程关联的student:
        std = local_school.student
        print('Hello, %s (in %s)' % (std, threading.current_thread().name))

    def process_thread(name):
        # 绑定ThreadLocal的student:
        local_school.student = name
        process_student()

    t1 = threading.Thread(target= process_thread, args=('Alice',), name='Thread-A')
    t2 = threading.Thread(target= process_thread, args=('Bob',), name='Thread-B')
    t1.start()
    t2.start()
    t1.join()
    t2.join()
    ```

<br>

### 正则表达式
```python
import re
re.match(r'^(\d+)(0*)$', '102300').groups()  # ('102300', '')

re.findall(r"{{.*?}}", "{{abc}}") # ['{{abc}}']

re.split(r'\s+', 'a b   c') # ['a', 'b', 'c']


# match()方法判断是否匹配，如果匹配成功，返回一个Match对象，否则返回None。常见的判断方法就是：

test = '用户输入的字符串'
if re.match(r'正则表达式', test):
    print('ok')
else:
    print('failed')
```

<br>

### 小技巧
```python
# 判断是否为数字，可以用于int()转换场景中
"123".isdigit()


# 带T的日期格式
format = "%Y-%m-%dT%H:%M:%S.%fZ" # 2018-06-12T04:55:22.000000Z


# 追加路径搜索范围
import os
import sys

sys.path.append(os.getcwd())


# 获取环境变量
key = os.getenv("ROBOT_KEY")
env = os.getenv("TEST_ENV", "dev")


# 移除空格
import re
re.split(r'\s+', 'a b   c') # ['a', 'b', 'c']


# 设置默认值
def func(*args, **kwargs):
    kwargs.setdefault("headers", {"User-Agent": const.USER_AGENT})


# 执行系统命令
import subprocess
result = subprocess.run(["ls", "-l"], stdout=subprocess.PIPE, stderr=subprocess.PIPE, text=True)
print(result.stdout)

# 把输出写入文件
with open("out.txt", "w") as f:
    result = subprocess.run(["ls", "-l"], stdout=f, stderr=subprocess.PIPE, text=True)

# 轮询结果
result = subprocess.Popen(["ping", "-n", "10", "bing.com"], stdout=subprocess.PIPE, stderr=subprocess.PIPE, text=True)

while not result.poll():
    sleep(1)

stdout, stderr = result.communicate()
print(result.stdout)
```

<br>
<br>

### 相关拓展
- [Python语言规范](https://zh-google-styleguide.readthedocs.io/en/latest/google-python-styleguide/python_language_rules/)
- [字符串和编码](https://www.liaoxuefeng.com/wiki/1016959663602400/1017075323632896)