
## 标题


# Heading level 1
## Heading level 2
### Heading level 3
#### Heading level 4
##### Heading level 5
###### Heading level 6

<br><br>

## 段落


<p>段落一</p>
<p>段落二</p>

<br><br>

## 换行

这是第一行<br><br>
这是第二行


<br><br>

## 着重


**前后各两个星号表示粗体**

*前后各一个星号表示斜体*

***前后各三个星号表示斜体和加粗***


<br><br>

## 删除线


~~前后各两个波浪线表示删除线~~


<br><br>

## 任务清单

- [x] 跑步
- [ ] 看电视
- [ ] 阅读


<br><br>

## 块引用

### 单个段落块
> 这是一个单个段落的块引用

<br>

### 多个段落块
> 这是一个具有多个段落的块引用
> 
> 在段落之间的空白行上添加一个 > 即可

<br>

### 嵌套段落块
> 这是一个嵌套的块引用 - 父
> 
>> 这是一个嵌套的块引用 - 子

<br>

### 混合多种样式的代码块
> 这是一个包含了多种样式的块引用
> #### Head4
>> *年龄*
>>
>> **姓名**


<br><br>

## 列表

### 有序列表

1. 有序列表
2. 有序列表
3. 有序列表
4. 有序列表

<br>

### 带有缩进的有序列表
1. 带有缩进的有序列表
   1. 缩进1
   2. 缩进2
2. 带有缩进的有序列表

<br>

### 无序列表

- 无序列表
- 无序列表
- 无序列表
- 无序列表

<br>

### 混合多种样式的列表

- 这是第一个列表项
- 这是第二个列表项

    要在保留列表连续性的同时在列表中添加另一个元素，请将该元素缩进四个空格或一个制表符

- 这是第三个列表项
    > 在第三个列表项的下方加一个块引用

<br><br>

## 代码块

### html
```html
<html>
  <head>
    <title>Test</title>
  </head>
```

### javascript
```javascript
function foo(params){
    console.log("hello");
}
```

### json
```json
{
  "firstName": "John",
  "lastName": "Smith",
  "age": 25
}
```

### python
```python
import arrow
from .. import const
from datetime import timedelta


def get_unixtime(length: int = 10) -> int:

    if length == 10:
        return int(arrow.now(tz=const.TZINFO).timestamp())

    elif length == 13:
        return int(arrow.now(tz=const.TZINFO).timestamp() * 1000)

```

### java
```java
public class HelloWorld {

    public static void main(String[]args){
       System.out.printIn("Hello World!");
    }
}
```

### shell
```shell
echo "hello world" > hello.txt
```

## sql
```sql
select * from user;
```

### text
```text
hello world
```

### css
```css
textarea {
    font-family:inherit;
    font-size:inherit;
    font-weight:inherit;
    vertical-align: baseline;
    *vertical-align: middle;
}
```

### xml
```xml
<div class="user-icon-content no-login">
    <div class="user-icon"></div>
    <div class="user-text">登录</div>
</div>
```

### 单行代码块
这是一个在单行中插入代码块的场景，如使用命令`pip install WeTest` 安装库。


<br><br>

## 水平分割线

---


<br><br>

## 链接

### URL或电子邮件地址快速转换为链接
<https://markdown.p2hp.com>

<fake@example.com>

<br>

### 加粗链接

加粗链接 **[EFF](https://eff.org)**.

斜体链接 *[Markdown Guide](https://markdown.p2hp.com)*.

<br>

### 自动网址链接

https://baiyyee.github.io/


### 禁用自动URL链接

`https://baiyyee.github.io/`

<br><br>

## 转义字符

\* 如果没有反斜杠，这将是无序列表中的项目符号


<br><br>

## 表格

要添加表，请使用三个或多个连字符（---）创建每列的标题，并使用管道（|）分隔每列。您可以选择在表的任一端添加管道。

| ID  | 姓名 | 年龄 |
| --- | ---- | ---- |
| 1   | HHB  | 18   |

<br>

### 对齐
| ID       |   姓名   |                                      身份证 |
| :------- | :------: | ------------------------------------------: |
| 1        |   HHB    |                        18000000000000000000 |
| 99999999 | Huabo He | 1000000000000000000000000000000000000000000 |
| 左对齐   | 居中对齐 |                                      右对齐 |


<br><br>

## 脚注


Docker 一般是部署到Linux[^linux]服务器上。

[^linux]: Linux，全称GNU/Linux，是一种免费使用和自由传播的类UNIX操作系统，其内核由林纳斯·本纳第克特·托瓦兹于1991年10月5日首次发布，它主要受到Minix和Unix思想的启发，是一个基于POSIX的多用户、多任务、支持多线程和多CPU的操作系统。它能运行主要的Unix工具软件、应用程序和网络协议。它支持32位和64位硬件。Linux继承了Unix以网络为核心的设计思想，是一个性能稳定的多用户网络操作系统。Linux有上百种不同的发行版，如基于社区开发的debian、archlinux，和基于商业开发的Red Hat Enterprise Linux、SUSE、Oracle Linux等。


<br><br>

## 图片

### 图片
![Linux Icon](../../../assets/image/linux.svg)

<br>

### 图片 (包含hover on文案)
![Linux Icon](../../../assets/image/linux.svg "图片hover on文案")

<br>

### 链接图像 (点击跳转)
[![Linux Icon](../../../assets/image/linux.svg "图片hover on文案")](https://www.linux.org/)

<br><br>


## Reference: 

- [Markdown 基本语法](http://markdown.p2hp.com/basic-syntax/index.html)
- [Markdown 扩展语法](http://markdown.p2hp.com/extended-syntax/index.html#fenced-code-blocks)