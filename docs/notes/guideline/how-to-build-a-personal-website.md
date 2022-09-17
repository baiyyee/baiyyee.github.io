# 如何快速搭建一个个人网站？


### 前置条件
> 
> 1. 可访问 Github 网站，且已注册完成账号
> 2. 熟悉 Git 操作，并已配置本地机器与 Github 的关联
> 3. 熟悉使用 pip 安装 python 包
> 4. 熟悉 Markdown语法
> 5. 通读 [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/getting-started) 文档


<br>

### 操作步骤

1.通过 pip 安装 mkdocs-material
```
pip install mkdocs-material
```

2.切换到指定目录，执行 `mkdocs new .` 生成目标站点的默认层级结构
```
.
├─ docs/
│  └─ index.md
└─ mkdocs.yml
```

3.更新 `mkdocs.yml` 文件，新增如下代码来修改默认主题配色
```
theme:
    name: material
```

4.执行 `mkdocs serve` 来启动一个本地站点服务，预览站点部署效果
```
mkdocs serve
INFO     -  Building documentation...
INFO     -  Cleaning site directory
INFO     -  Documentation built in 0.08 seconds
INFO     -  [20:57:04] Watching paths for changes: 'docs', 'mkdocs.yml'
INFO     -  [20:57:04] Serving on http://127.0.0.1:8000/
```

5.点击链接 <http://127.0.0.1:8000/> 即可访问本地站点
![默认站点效果预览](https://squidfunk.github.io/mkdocs-material/assets/screenshots/creating-your-site.png)


6.参照 [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/getting-started) 文档自定义站点样式，内容，导航等


7.在Github上新建一个仓库，命名为固定的 **{username}.github.io**
   > NOTE: username 需要替换成你的 Github 用户名

8.完成自定义且预览效果符合预期后，进入站点根目录执行 `git push` 将站点文件夹内容推送到 Github **{username}.github.io** 仓库

9.进入到 Github **{username}.github.io** 仓库，依次点击 `ACTION -> new workflow -> Simple workflow`, 输入以下内容后提交保存：
```
name: deploy website
on:
push:
    branches:
    - main
jobs:
deploy:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - uses: actions/setup-python@v2
        with:
        python-version: 3.x
    - run: pip install mkdocs-material 
    - run: mkdocs gh-deploy --force
```

10.进入到 Github **{username}.github.io** 仓库，依次点击 `Settings -> Pages -> Branch`, 将 Branch 字段由 `main` 更新为 `gh-pages` 后保存


11.在本地更新任意一个文件内容并提交后，重新执行 `git push`，推送完成后将自动触发 Github Action

12.等待 Github Action 执行完成后，访问 **https://{username}.github.io** 即可访问已部署的站点，访问效果与本地预览效果一致



<br><br>

参考样例：<https://github.com/baiyyee/baiyyee.github.io>
