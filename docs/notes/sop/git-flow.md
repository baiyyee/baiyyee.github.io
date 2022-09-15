# Git Flow


## git commit


- 提交格式

```xml
<type>(<scope>): <subject>

<body>

<footer>
```


> 备注：
>
> 标题行: 必填, 描述主要修改类型和内容
>
> 主题内容: 描述为什么修改, 做了什么样的修改, 以及开发的思路等等
>
> 页脚注释: 放 Breaking Changes 或 Closed Issues



<br>

- 字段说明
    - type
  
        | 提交类型 | 类型描述                                                                                            |
        | -------- | --------------------------------------------------------------------------------------------------- |
        | feat     | 新功能、新特性                                                                                      |
        | fix      | 修改 bug                                                                                            |
        | perf     | 更改代码，以提高性能（在不影响代码内部行为的前提下，对程序性能进行优化）                            |
        | refactor | 代码重构（重构，在不影响代码内部行为、功能下的代码修改）                                            |
        | docs     | 文档修改                                                                                            |
        | style    | 代码格式修改, 注意不是 css 修改（例如分号修改）                                                     |
        | test     | 测试用例新增、修改                                                                                  |
        | build    | 影响项目构建或依赖项修改                                                                            |
        | revert   | 恢复上一次提交                                                                                      |
        | ci       | 持续集成相关文件修改                                                                                |
        | chore    | chore 的中文翻译为日常事务、例行工作，顾名思义，即不在其他 commit 类型中的修改，都可以用 chore 表示 |
        | release  | 发布新版本                                                                                          |
        | workflow | 工作流相关文件修改                                                                                  |

    - scope
        - commit 影响的范围, 比如: route, component, utils, build...

    - subject
        - 对本次提交变更的描述说明

    - body
        - commit 具体修改内容, 可以分为多行

    - footer
        - 一些备注, 通常是 BREAKING CHANGE 或修复的 bug 的链接.

<br>

- 提交规范
    - 每个提交都必须使用类型字段前缀，它由一个名词组成，诸如 feat 或 fix ，其后接一个可选的作用域字段，以及一个必要的冒号（英文半角）和空格
    - 当一个提交为应用或类库实现了新特性时，必须使用 feat 类型
    - 当一个提交为应用修复了 bug 时，必须使用 fix 类型
    - 作用域字段可以跟随在类型字段后面。作用域必须是一个描述某部分代码的名词，并用圆括号包围，例如： fix(parser):
    - 描述字段必须紧接在类型/作用域前缀的空格之后。描述指的是对代码变更的简短总结，例如： fix: array parsing issue when multiple spaces were contained in string.
    - 在简短描述之后，可以编写更长的提交正文，为代码变更提供额外的上下文信息。正文必须起始于描述字段结束的一个空行后
    - 在正文结束的一个空行之后，可以编写一行或多行脚注。脚注必须包含关于提交的元信息，例如：关联的合并请求、Reviewer、破坏性变更，每条元信息一行
    - 破坏性变更必须标示在正文区域最开始处，或脚注区域中某一行的开始。一个破坏性变更必须包含大写的文本 BREAKING CHANGE，后面紧跟冒号和空格
    - 在 BREAKING CHANGE: 之后必须提供描述，以描述对 API 的变更。例如： BREAKING CHANGE: environment variables now take precedence over config files.
    - 在提交说明中，可以使用 feat 和 fix 之外的类型
    - 工具的实现必须不区分大小写地解析构成约定式提交的信息单元，只有 BREAKING CHANGE 必须是大写的
    - 可以在类型/作用域前缀之后，: 之前，附加 ! 字符，以进一步提醒注意破坏性变更。当有 ! 前缀时，正文或脚注内必须包含 BREAKING CHANGE: description

<br>

- 参考示例
```
fix
如果修复的这个BUG只影响当前修改的文件，可不加范围。如果影响的范围比较大，要加上范围描述。
例如这次 BUG 修复影响到全局，可以加个 global。如果影响的是某个目录或某个功能，可以加上该目录的路径，或者对应的功能名称

// 示例1
fix(global):修复checkbox不能复选的问题

// 示例2 - 下面圆括号里的 common 为通用管理的名称
fix(common): 修复字体过小的BUG，将通用管理下所有页面的默认字体大小修改为 14px

// 示例3
fix: value.length -> values.length

// 示例4
feat: 添加网站主页静态页面

// 示例5
chore: 将表格中的查看详情改为详情
```


<br><br>
引用：<https://zhuanlan.zhihu.com/p/90281637>