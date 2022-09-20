# python



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

### 字典
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