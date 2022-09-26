# pandas



### DataFrame
```python
import pymysql
import pandas as pd

# 读取CSV文件
path_ratings = "./ratings.csv"
ratings = pd.read_csv(path_ratings)

# 此处指定 engine="python" 是为了避免将分隔符"::" 识别为正则匹配
# ratings = pd.read_csv("./ratings.csv", sep="::", engine="python", names="UserId,MovieId,Rating,Timestamp".split(","))


# 读取TAB分割的txt文件
path_access = "./access_pvuv.txt"
access = pd.read_csv(path_access, sep="\t", header=None, names=["pdate", "pv", "uv"])


# 读取Excel文件
path_access = "./access_pvuv.xlsx"
access = pd.read_excel(path_access)

# 读取MySQL数据库
conn = pymysql.connect(host="127.0.0.1", user="root", password="123456", database="test", charset="utf8")
data = pd.read_sql("select * from access_pvuv", con=conn)


# 全部数据
ratings

# 查看前几行
ratings.head()
ratings.head(1)


# 查看数据形状，返回(行，列)
ratings.shape

# 查看数据列名列表
ratings.columns

# 查看索引信息
ratings.index

# 查看每列的数据类型
ratings.dtypes



# 根据多个字典序列创建DataFrame

data = {"name": ["Bob", "John", "Roy", "Joe"], "age": [18, 19, 20, 21], "height": [165, 178, 180, 181]}
df = pd.DataFrame(data)
df


# 查询列
df["name"]  # 查询一列，结果是一个 Series
df[["name", "age"]]  # 查询多列，结果是一个 DataFrame


# 查询行
df.loc[1]  # 查询单行
df.loc[1:3]  # 查询多行
```

<br>


### Series
```python
import pandas as pd

# 使用默认索引
series = pd.Series([1, "a", 5.2, 77777])
series

# 自定义索引
series = pd.Series([1, "a", 5.2, 77777], index=["a", "b", "c", "d"])
series

# 使用字典创建 Series
data = {"name": "hhb", "age": 18, "height": 180, "weight": 60}
series = pd.Series(data)
series

# 查看索引
series.index

# 查看值
series.values


# 获取值
series["name"]

# 获取多个值
series[["name", "age"]]

# 查看类型
type(series["name"])  # => str
type(series[["name", "age"]])  # => pandas.core.series.Series
```

<br>

### 数据查询
```python
import pandas as pd

path = "./beijing_tianqi_2018.csv"
df = pd.read_csv(path)
df.head()

# 重新设置索引，代替默认的索引
df.set_index("日期", inplace=True)
df.index


# 数据清洗：移除温度后缀并转化为int类型
df.loc[:, "最低温度"] = df["最低温度"].str.replace("℃", "").astype("int32")
df.loc[:, "最高温度"] = df["最高温度"].str.replace("℃", "").astype("int32")
df.dtypes

# 使用Label查询

# 按行和列精确查找，得到单个值
df.loc["2018-01-01", "最高温度"]

# 查询某一行多列值
df.loc["2018-01-01", ["最高温度", "最低温度"]]

# 查询某一列多行的值
df.loc[["2018-01-01", "2018-01-02"], "最高温度"]

# 查询多行多列的值
df.loc[["2018-01-01", "2018-01-02"], ["最高温度", "最低温度"]]

# 使用索引区间查值
df.loc["2018-01-01":"2018-01-05", "最高温度":"风级"]

# 使用条件表达式查值
df.loc[df["最低温度"] < -5, :]  # => 查询最低温度小于-5度的数据全部列记录

# 查看过滤条件返回的内容
df["最低温度"] < -5


# 多条件组合查询
df.loc[(df["最高温度"] < 11) & (df["最低温度"] > -7) & (df["天气"] == "晴"), :]


# 使用函数查询

# 自定义函数查询
def query_data(data):
    return (data["最高温度"] < 11) & (data["最低温度"] > -7) & (data["天气"] == "晴")


df.loc[query_data, :]

# 使用lambda查询
df.loc[lambda data: (data["最高温度"] < 11) & (data["最低温度"] > -7) & (data["天气"] == "晴"), :]
df.loc[lambda data: (data.index.str.startswith("2018-01-01")), :]  # 了解转字符串后函数的使用
```

<br>

### 新增列
```python
import pandas as pd

path = "./beijing_tianqi_2018.csv"
df = pd.read_csv(path)

# 方法一
df.loc[:, "最低温度"] = df["最低温度"].str.replace("℃", "").astype("int32")
df.loc[:, "最高温度"] = df["最高温度"].str.replace("℃", "").astype("int32")

# 方法二
# df["最低温度"] = df["最低温度"].map(lambda x: int(str(x).replace("℃", "")))
# df["最高温度"] = df["最高温度"].map(lambda x: int(str(x).replace("℃", "")))

# 新增列

# 方法一
df["温差"] = df["最高温度"] - df["最低温度"]

# 方法二
df.loc[:, "温差"] = df["最高温度"] - df["最低温度"]


# axis = 0 => index
# axis = 1 => column

# 使用 apply 函数
# 新增一列【体表感觉】，大于0度显示为感觉还好，低于0度显示为好冷


def get_feel(data):
    if data["最高温度"] > 0:
        return "感觉还好"
    else:
        return "有点冷"


df["体表感觉"] = df.apply(get_feel, axis=1)
df["体表感觉"].value_counts()  # 计数函数


# 使用 assign 函数新增列（可同时新增多列）

# 将温度从摄氏度转化为华氏度
df.assign(最高温度_华氏度=lambda data: data["最高温度"] * 9 / 5 + 32, 最低温度_华氏度=lambda data: data["最低温度"] * 9 / 5 + 32)


# 按条件选择分组分别赋值
df["温差类型"] = ""  # pandas 广播功能
df.loc[df["最高温度"] - df["最低温度"] > 10, "温差类型"] = "温差大"
df.loc[df["最高温度"] - df["最低温度"] <= 10, "温差类型"] = "温差正常"


# 使用 apply 函数同时新增多列


def my_func(df):
    # 同时新增 温差 和 平均温度 两列
    return df["最高温度"] - df["最低温度"], (df["最高温度"] - df["最低温度"]) / 2


df[["Wencha", "AVG"]] = df.apply(my_func, axis=1, result_type="expand")
```

<br>

### 数据统计
```python
import pandas as pd

path = "./beijing_tianqi_2018.csv"
df = pd.read_csv(path)

df.loc[:, "最低温度"] = df["最低温度"].str.replace("℃", "").astype("int32")
df.loc[:, "最高温度"] = df["最高温度"].str.replace("℃", "").astype("int32")

# 汇总类统计
df.describe()  # 计数，平均值，标准差，最小值，分位数，最大值

# 查看单列值的统计
df["最高温度"].mean()
df["最高温度"].max()
df["最高温度"].std()
df["最高温度"].min()


# 唯一去重
df["天气"].unique()

# 按值计数
df["天气"].value_counts()


# 协方差矩阵 => 衡量同向反向程度
df.cov()

# 相关系数矩阵 => 衡量相似度程度
df.corr()

# 单独查看某些列的相关系数
df["最高温度"].corr(df["aqi"])
(df["最高温度"] - df["最低温度"]).corr(df["aqi"])
```

<br>

### 缺失值处理
```python
import pandas as pd

path = "./student_score.xlsx"
df = pd.read_excel(path, skiprows=2)


# 为空/非空 判断
df["分数"].isnull()
df["分数"].notnull()


# 删除数据全为空的列
df.dropna(axis="columns", how="all", inplace=True)


# 删除数据全为空的行
df.dropna(axis="index", how="all", inplace=True)


# 将分数为空的列填充为0

# 方法一：
df.fillna({"分数": 0})
# 方法二：
df.loc[:, "分数"] = df["分数"].fillna(0)
# 方法三：
df["分数"] = df["分数"].fillna(0)


# 将姓名列合并单元格的值进行填充
# ffill => forward fill 向前填充
df["姓名"] = df["姓名"].fillna(method="ffill")

df.to_excel("./student_score_new.xlsx", index=False)
```

<br>

### 报警处理
```python
import pandas as pd

path = "./beijing_tianqi_2018.csv"
df = pd.read_csv(path)

df.loc[:, "最低温度"] = df["最低温度"].str.replace("℃", "").astype("int32")
df.loc[:, "最高温度"] = df["最高温度"].str.replace("℃", "").astype("int32")

# SettingWithCopyWarning
df[df["日期"].str.startswith("2018-01")]["温差"] = df["最高温度"] - df["最低温度"]


# 解决方案：将 get + set 的两步操作，改成 set 的一步操作
df.loc[df["日期"].str.startswith("2018-01"), "温差"] = df["最高温度"] - df["最低温度"]
```

<br>

### 数据排序
```python
import pandas as pd

path = "./beijing_tianqi_2018.csv"
df = pd.read_csv(path)

df.loc[:, "最低温度"] = df["最低温度"].str.replace("℃", "").astype("int32")
df.loc[:, "最高温度"] = df["最高温度"].str.replace("℃", "").astype("int32")

# 默认是升序排序
df["最高温度"].sort_values()
# 降序排序
df["最高温度"].sort_values(ascending=False)

# 将整个DataFrame 按 最高温度升序排序
df.sort_values(by="最高温度")
df.sort_values(by="最高温度", ascending=False)

# 多列排序
df.sort_values(by=["最高温度", "最低温度"])
# 多列排序且分别指定升序和降序
df.sort_values(by=["最高温度", "最低温度"], ascending=[True, False])
df.sort_values(by=["最高温度", "最低温度"], ascending=[True, False], inplace=True)
```

<br>

### 字符串处理
```python
import pandas as pd

path = "./beijing_tianqi_2018.csv"
df = pd.read_csv(path)


df["天气"].str

# 常用方法
df["最低温度"].str.replace("℃", "")
df["最低温度"].str.isnumeric()
df["最低温度"].str.len()

# NOTE: 只能在字符串上调用 .str 属性


# .str 方法用于条件查询
df[df["日期"].str.startswith("2018-01")]

# .str 的链式操作
df["日期"].str.replace("-", "").str.slice(0, 6)
df["日期"].str.replace("-", "").str[0:6]


# 添加新列
def get_nianyueri(data):
    year, month, day = data["日期"].split("-")
    return f"{year}年{month}月{day}日"


df["中文日期"] = df.apply(get_nianyueri, axis=1)

# 将中文日期列中的年月日移除

# 方法一：链式调用
df["中文日期"].str.replace("年", "").str.replace("月", "").str.replace("日", "")

# 方法二：使用正则表达式
df["中文日期"].str.replace("[年月日]", "")
```

<br>

### axis
```python
import pandas as pd
import numpy as np


df = pd.DataFrame(np.arange(12).reshape(3, 4), columns=["A", "B", "C", "D"])

# 删除单列
df.drop("A", axis=1)

# 删除单行
df.drop(1, axis=0)

# 对行取平均值
df.mean(axis=0)
# 对列取平均值
df.mean(axis=1)


def get_sum_value(data):
    return data["A"] + data["B"] + data["C"] + data["D"]


df["sum"] = df.apply(get_sum_value, axis=1)
```

<br>

### index
```python
import pandas as pd

df = pd.read_csv("./ratings.csv")

# 列数据计数
df.count()

# 设置索引
df.set_index("userId", inplace=False, drop=True)

# 使用 index 查询数据
df.loc[1]

df.loc[df["userId"] == 1]


# 使用 index 会提升查询性能:
#   index 唯一 => 哈希优化 => O(1)
#   index 不唯一但有序 => 二分查找 => O(logN)
#   index 完全随机 => 扫描全表 => O(N)

# 启发：
# 1. 尽量令 index 唯一
# 2. 尽量令 index 有序

# 判断索引是否单调递增
df.index.is_monotonic_increasing

# 判断索引是否唯一
df.index.is_unique


# %timeit 函数为IPyton中的一个内置函数，用于计算代码执行时间，会将命令执行很多次获取平均性能

#%timeit df => 23.3 ns ± 1.94 ns per loop (mean ± std. dev. of 7 runs, 10000000 loops each)

# 对索引进行排序
df.sort_index()


s1 = pd.Series([1, 2, 3], index=list("abc"))
s2 = pd.Series([2, 3, 4], index=list("bcd"))

# 索引自动对齐，相同的索引值会进行加和
s1 + s2


# 很多强大的索引数据结构
#   CAtegoricalIndex : 分类索引
#   MutableIndex : 多维索引
#   DatetimeIndex : 日期索引
```

<br>

### merge
```python
# Pandas 中的 Merge 操作相当于 SQL 中的 Join 操作

# left, right: 合并的两个 DataFrame
# how: join 类型，可选值为 inner, left, right, outer
# on: join 的 key, 可以是一个列名，也可以是一个列表
# left_on, right_on: 分别对应 left 和 right 的 key
# left_index, right_index: 使用index进行join，而不是普通的columns
# suffixes: 分别对应 left 和 right 的后缀


import pandas as pd

ratings = pd.read_csv("./ratings.csv")
users = pd.read_csv("./users.csv")
movies = pd.read_csv("./movies.csv")


# 内连接 - 交集
ratings_users_inner = pd.merge(ratings, users, left_on="UserId", right_on="UserId", how="inner")

# 左连接 - 左表的所有行
ratings_users_left = pd.merge(ratings, users, left_on="UserId", right_on="UserId", how="left")

# 右连接 - 右表的所有行
ratings_users_right = pd.merge(ratings, users, left_on="UserId", right_on="UserId", how="right")

# 全连接 - 并集
ratings_users_outer = pd.merge(ratings, users, left_on="UserId", right_on="UserId", how="outer")


# 非 key 的字段重名时可自定义后缀予以区分

left = pd.DataFrame({"key": ["K0", "K1", "K2", "K3"], "A": ["A0", "A1", "A2", "A3"], "B": ["B0", "B1", "B2", "B3"]})
right = pd.DataFrame({"key": ["K0", "K1", "K2", "K3"], "A": ["A10", "A11", "A12", "A13"], "D": ["D0", "D1", "D2", "D3"]})

# 默认后缀
pd.merge(left, right, on="key")

# 自定义后缀
pd.merge(left, right, on="key", suffixes=("_left", "_right"))
```

<br>

### concat
```python
# 批量合并相同格式的Excel，给DataFrame添加行，给DataFrame添加列

# objs: 合并对象列表，可以是DataFrame或Se
# axis: 合并轴，0为行，1为列
# join: 合并方式，可选值为inner, outer
# ignore_index: 是否忽略索引

import pandas as pd

df1 = pd.DataFrame(
    {
        "A": ["A0", "A1", "A2", "A3"],
        "B": ["B0", "B1", "B2", "B3"],
        "C": ["C0", "C1", "C2", "C3"],
        "D": ["D0", "D1", "D2", "D3"],
        "E": ["E0", "E1", "E2", "E3"],
    }
)
df2 = pd.DataFrame(
    {
        "A": ["A4", "A5", "A6", "A7"],
        "B": ["B4", "B5", "B6", "B7"],
        "C": ["C4", "C5", "C6", "C7"],
        "D": ["D4", "D5", "D6", "D7"],
        "F": ["F4", "F5", "F6", "F7"],
    }
)

# 数据合并
pd.concat([df1, df2])
# 使用 ignore_index=True 忽略原来的索引
pd.concat([df1, df2], ignore_index=True)


# 使用 join="inner" 过滤掉不匹配的列
pd.concat([df1, df2], ignore_index=True, join="inner")


# 使用 axis=1 新增列
s1 = pd.Series(list(range(4)), name="F")
pd.concat([df1, s1], axis=1)


# 添加多列 Series
s2 = df1.apply(lambda x: x["A"] + "_GG", axis=1)
s2.name = "G"
pd.concat([df1, s1, s2], axis=1)


# 调整合并顺序
pd.concat([s1, df1, s2], axis=1)
```

<br>

### Append
```python
import pandas as pd

df1 = pd.DataFrame([[1, 2], [3, 4]], columns=["A", "B"])
df2 = pd.DataFrame([[5, 6], [7, 8]], columns=["A", "B"])

df1.append(df2)

# 使用 ignore_index=True 忽略原来的索引
df1.append(df2, ignore_index=True)

# 使用循环一行一行给DataFrame添加数据
pd.concat([pd.DataFrame([i], columns=["A"]) for i in range(10)], ignore_index=True)
```

<br>

### 批量拆分于合并Excel文件
```python
import os
import pandas as pd

if not os.path.exists("./splits"):
    os.mkdir("./splits")


source = pd.read_excel("./source.xlsx")

source.shape  # 返回DataFrame的 (行，列)

# 将大的Excel拆分给不同的人（模拟任务划分）
user_names = ["user" + str(i) for i in ["A", "B"]]

# 每个人分到的任务数
size = int(source.shape[0] // len(user_names))

# 如果任务不是均分的话，需要把多余的任务放到最后一个人
if int(source.shape[0] % len(user_names)) != 0:
    size += 1


subs = []
for index, user in enumerate(user_names):
    begin = index * size
    end = begin + size
    df_sub = source.iloc[begin:end]
    subs.append((index, user, df_sub))

for index, user, df_sub in subs:
    df_sub.to_excel("./splits/{}_{}.xlsx".format(index, user), index=False)


# 合并多个小的Excel文件到一个大的Excel

import os

files = []
for file in os.listdir("./splits"):
    files.append("./splits/" + file)


df_list = []
for file in files:
    df = pd.read_excel(file)
    user_name = file.split("_")[1].split(".")[0]

    df["username"] = user_name

    df_list.append(df)

df_merged = pd.concat(df_list, ignore_index=True)
df_merged.to_excel("./source_merged.xlsx", index=False)
```

<br>

### groupby
```python
import pandas as pd
import numpy as np

df = pd.DataFrame(
    {
        "A": ["foo", "bar", "foo", "bar", "foo", "bar", "foo", "foo"],
        "B": [1, 1, 2, 3, 2, 2, 1, 3],
        "C": np.random.randn(8),
        "D": np.random.randn(8),
    }
)


# 单列求和
df.groupby("A").sum()
# 多列求和
df.groupby(["A", "B"]).sum()

# 取消二级索引
df.groupby(["A", "B"], as_index=False).sum()

# 同时查看多种数据统计：求和，求平均值，求标准差
df.groupby("A").agg([np.sum, np.mean, np.std])

# 查看单列统计结果
# 方法一
df.groupby("A")["C"].agg([np.sum, np.mean, np.std])
# 方法二
df.groupby("A").agg([np.sum, np.mean, np.std])["C"]

# 对不同的列进行不同的统计(使用不同的聚合函数)
df.groupby("A").agg({"C": np.sum, "D": np.mean})
```

<br>

### 分层索引
```python
# MultiIndex => 分层索引

import pandas as pd
import numpy as np

df = pd.read_excel("./公司股票.xlsx")

# 查看公司
df["公司"].unique()

# 查看每个公司多日收盘价的平均值
df.groupby("公司")["收盘"].mean()

# 查看每个公司单日收盘价的平均值
df.groupby(["日期", "公司"])["收盘"].mean()
# 多维索引中，空白的意思是：使用上面的值

# 查看索引
df.groupby(["日期", "公司"])["收盘"].mean().index

# unstack 把二级索引变成列
df.groupby(["日期", "公司"])["收盘"].mean().unstack()

# 使用重置索引把二级索引变成列
df.groupby(["日期", "公司"])["收盘"].mean().reset_index()

# 从多层索引中获取数据
df.groupby(["日期", "公司"])["收盘"].mean().index

# 获取第一层索引值
df.groupby(["日期", "公司"])["收盘"].mean()["2019-10-01"]
# 获取第二层索引值
df.groupby(["日期", "公司"])["收盘"].mean()["2019-10-01"]["京东"]
# 多层索引可以使用元组的形式筛选
df.groupby(["日期", "公司"])["收盘"].mean().loc[("2019-10-01", "京东")]

# 获取第二层索引的值
df.groupby(["日期", "公司"])["收盘"].mean().loc[:, "京东"]


# 自定义分层索引
df.set_index(["日期", "公司"])

# 按索引排序
df.set_index(["日期", "公司"]).sort_index(inplace=True)

# NOTE:
# 元组(key1, key2) 代表多层索引，其中 key1 是第一层，key2 是第二层
# 列表[key1, key2] 代表同一层的多个key，其中key1和key2是并列的同级索引

df_new = df.set_index(["日期", "公司"])
df_new.sort_index(inplace=True)


df_new.loc["2019-10-01"]
df_new.loc[("2019-10-01", "京东")]
df_new.loc[("2019-10-01", "京东"), "收盘"]
df_new.loc[(["2019-10-01", "2019-10-02"], "京东"), :]
df_new.loc[(["2019-10-01", "2019-10-02"], ["京东", "百度"]), :]
df_new.loc[(["2019-10-01", "2019-10-02"], ["京东", "百度"]), ["开盘", "收盘"]]
# slice(None) 表示筛选这一索引列所有的内容
df_new.loc[(["2019-10-01", "2019-10-02"], slice(None)), :]
df_new.loc[(slice(None), ["京东", "百度"]), :]
```

<br>

### 数据转换函数
```python
# map: 只适用于Series，实现每个值 -> 值 的映射
# apply: 用于Series实现每个值的处理；用于DataFrame实现某个轴的Series的处理
# applymap: 只能用于DataFrame，用于处理该DataFrame的每个元素

import pandas as pd
import numpy as np

df = pd.read_excel("./公司股票.xlsx")

df["公司"].unique()

# 将公司名称从中文映射为拼音
name_mapping = {"百度": "BD", "阿里": "AL", "爱奇艺": "iQiyi", "京东": "JD"}

# 方法一：使用字典
df["公司-英文01"] = df["公司"].map(name_mapping)

# 方法二：使用函数
df["公司-英文02"] = df["公司"].map(lambda x: name_mapping[x])


# Series - apply
df["公司-英文03"] = df["公司"].apply(lambda x: name_mapping[x])

# DataFrame - apply
df["公司-英文04"] = df.apply(lambda x: name_mapping[x["公司"]], axis=1)


# applymap 用于DataFrame所有值的转换(不是某一行某一列，是所有的值)
sub_df = df[["收盘", "开盘", "高", "低", "交易量"]]

# 将"收盘", "开盘", "高", "低", "交易量"从小数转为整数
df.loc[:, ["收盘", "开盘", "高", "低", "交易量"]] = sub_df.applymap(lambda x: int(x))
```

<br>

### apply
```python
# pandas 的 groupby 遵从 split, apply, combine 模式

# 归一化：将不同范围的数值映射到 [0,1] 区间
# 归一化公式：X_normalized = (X - X_min) / (X_max - X_min)

import pandas as pd

path_ratings = "./ratings.csv"
ratings = pd.read_csv(path_ratings)

# 按照用户ID分组并对评分列进行归一化处理


def guiyi(df):
    df["Rating"] = df["Rating"].apply(lambda x: int(x))
    min_value = df["Rating"].min()
    max_value = df["Rating"].max()
    df["Rating_normal"] = df["Rating"].apply(lambda x: (x - min_value) / (max_value - min_value))
    return df


ratings.groupby("UserId").apply(guiyi)


# 获取分组后的TopN数据
path_tianqi = "./beijing_tianqi_2018.csv"
df = pd.read_csv(path_tianqi)


df.loc[:, "最低温度"] = df["最低温度"].str.replace("℃", "").astype("int32")
df.loc[:, "最高温度"] = df["最高温度"].str.replace("℃", "").astype("int32")

# 新增一列月份
df["月份"] = df["日期"].str[:7]


def get_wendu_topN(df, n):
    return df.sort_values(by="最高温度")[["日期", "最高温度"]][-n:]


# 注意下面函数传参的方式
df.groupby("月份").apply(get_wendu_topN, n=2)
```

<br>

### 数据透视
```python
import pandas as pd
import numpy as np

path_ratings = "./ratings.csv"
df = pd.read_csv(path_ratings)


df["pdate"] = pd.to_datetime(df["Timestamp"], unit="s")

# 按月份和评分分组，统计PV
df_group = df.groupby([df["pdate"].dt.month, "Rating"])["UserId"].agg(pv=np.sum)

# 二维透视
df_group.unstack()


# stack 和 unstack 是互逆操作，可以互相转化
df_group.unstack().stack()


# pivot 方法相当于对 df 使用 set_index 创建分层索引，然后调用 unstack
df_reset = df_group.reset_index()
df_pivot = df_reset.pivot("pdate", "Rating", "pv")


# level=-1表示多层索引的最内层; 0，1，2分别代表第一层，第二层，第三层

# 语法：
# DataFrame.stack(level=-1, dropna=True) => 将 index 和 column 调换
# DataFrame.unstack(level=-1, fill_value=None) => 将 index 和 column 调换
# DataFrame.pivot(index=None, columns=None, values=None) => 指定index, columns, values实现二维透视
```

<br><br>


### 拓展阅读

- [Python pandas Tutorial](https://sparkbyexamples.com/python-pandas-tutorial-for-beginners/)