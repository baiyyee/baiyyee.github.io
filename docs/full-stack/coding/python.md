# python



```python
from functools import reduce

def fib(n):
    """斐波拉契数列"""
    if n == 1:
        return 1
    elif n == 2:
        return 2
    else:
        return fib(n - 1) + fib(n - 2)

def fib2(n):
    """斐波拉契数列"""
    i, a, b = 0, 0, 1
    while i < n:
        yield b
        a, b = b, a + b
        i += 1

for i in fib2(6):
    print(i)

def triangles(n):
    """杨辉三角"""
    L = [1]
    i = 0
    while i < n:
        yield L
        L = [1] + [L[i] + L[i + 1] for i in range(len(L) - 1)] + [1]
        i += 1

for i in triangles(6):
    print(i)

def sushu(n):
    """素数"""
    data = []
    for i in range(1, n):
        count = 0
        for j in range(1, i + 1):
            if i % j == 0:
                count += 1
                if count > 2:
                    break
        if count <= 2:
            data.append(i)

    return data

print(sushu(20))

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