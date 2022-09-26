# pypi

### 步骤

1.安装依赖 `pip`, `build`, `twine`
```
python3 -m pip install --upgrade pip
python3 -m pip install --upgrade build
python3 -m pip install --upgrade twine
```

2.按模板新建项目
```
packaging_tutorial/
├── LICENSE
├── pyproject.toml
├── README.md
├── src/
│   └── example_package_YOUR_USERNAME_HERE/
│       ├── __init__.py
│       └── example.py
└── tests/
```

3.构建项目
```
python3 -m build
```

4.注册 PyPI 账号, 按需创建Token (可用于和Github Actions联动来自动发包)
```
注册 PyPI 账号: https://pypi.org/account/register
创建Token: https://pypi.org/manage/account
```

5.上传项目
```
python3 -m twine upload dist/*
```

<br><br>

### 相关拓展
- [完整 Demo](https://github.com/baiyyee/WeTest)
- [Packaging Python Projects](https://packaging.python.org/en/latest/tutorials/packaging-projects/)
- [Github Actions 自动发布](https://github.com/baiyyee/WeTest/blob/main/.github/workflows/publish.yml)