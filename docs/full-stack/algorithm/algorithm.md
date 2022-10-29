# LeetCode


### 斐波拉契数列

```python
def fib(n):
    if n == 1:
        return 1
    elif n == 2:
        return 2
    else:
        return fib(n - 1) + fib(n - 2)

def fib2(n):
    i, a, b = 0, 0, 1
    while i < n:
        yield b
        a, b = b, a + b
        i += 1

for i in fib2(6):
    print(i)
```

<br>

### 杨辉三角

```python
def triangles(n):

    L = [1]
    i = 0
    while i < n:
        yield L
        L = [1] + [L[i] + L[i + 1] for i in range(len(L) - 1)] + [1]
        i += 1

for i in triangles(6):
    print(i)
```
<br>

### 素数

```python
def sushu(n):

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
```

<br>

### [二分查找](https://leetcode.cn/problems/binary-search/)
```python
# 解法一：递归
def search(self, nums: List[int], target: int) -> int:
    def search_num(nums, start, end, target):
        mid = (start + end) // 2

         if start <= end:
             if target > nums[mid]:
                 return search_num(nums, mid + 1, end, target)
             elif target < nums[mid]:
                 return search_num(nums, start, mid - 1, target)
             else:
                 return mid

         else:
             return -1

    return search_num(nums, 0, len(nums) - 1, target)

# 解法二：循环
def search(self, nums: List[int], target: int) -> int:

    start = 0
    end = len(nums) - 1

    while start <= end:
        mid = (start + end) // 2
        if target > nums[mid]:
            start = mid + 1
        elif target < nums[mid]:
            end = mid - 1
        else:
            return mid

    return -1
```

### [字符串解码](https://leetcode.cn/problems/decode-string/)
```python
def decodeString(self, s: str) -> str:
    stack, res, multi = [], "", 0

    for c in s:
        if c == "[":
            stack.append([multi, res])
            res, multi = "", 0

        elif c == "]":
            cur_muti, last_res = stack.pop()
            res = last_res + cur_muti * res

        elif "0" <= c <= "9":
            multi = multi * 10 + int(c)

        else:
            res += c

    return res
```

<br>

### [两数之和](https://leetcode.cn/problems/two-sum/)

```python
# 解法一
def twoSum(self, nums: List[int], target: int) -> List[int]:
    for i, num in enumerate(nums):
        for j in range(i + 1, len(nums)):
            if num + nums[j] == target:
                return i, j


# 解法二
def twoSum(self, nums: List[int], target: int) -> List[int]:
    temp = {}
    for i, v in enumerate(nums):

        if (target - v) in temp:
            return [temp.get(target - v), i]
        else:
            temp[v] = i
```


### [爬楼梯](https://leetcode.cn/problems/climbing-stairs/)
```python
# 解法一：Time Limit Exceeded - 存在重复计算问题
def climbStairs(self, n: int) -> int:
    if n == 1:
        return 1
    elif n == 2:
        return 2
    else:
        return self.climbStairs(n - 1) + self.climbStairs(n - 2)

# 解法二：存储已经计算的值，避免重复计算
data = {}
def climbStairs(self, n: int) -> int:
    if n == 1:
        return 1
    elif n == 2:
        return 2

    if n in self.data:
        return self.data[n]
    else:
        result = self.climbStairs(n - 1) + self.climbStairs(n - 2)
        self.data[n] = result
        return result

# 解法三：使用循环
def climbStairs(self, n: int) -> int:
    if n == 1:
        return 1
    elif n == 2:
        return 2

    result = 0
    pre = 2
    pre_pre = 1
    for i in range(3, n + 1):
        result = pre + pre_pre
        pre_pre = pre
        pre = result

    return result
```

### 回文字符串
```python
import re

def is_huiwenstring(data: str):
    pattern = r"[a-zA-Z0-9]"
    data = re.findall(pattern, data)

    data = "".join([d.lower() for d in data])

    if data[:] == data[::-1]:
        return True
    else:
        return False


data = "A man a plan a canal panama"
is_huiwenstring(data)
```

<br><br>


### 相关拓展

- [Leetcode Solution](https://github.com/youngyangyang04/leetcode-master)