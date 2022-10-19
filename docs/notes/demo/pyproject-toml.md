# pyproject.toml

```ini
[build-system]
requires = ["setuptools>=61.0"]
build-backend = "setuptools.build_meta"


[project]
name = "WeTest"
version = "1.0.6"
authors = [{ name="Huabo He", email="hhbstar@hotmail.com" }]
description = "Toolkit For Automated Testing"
readme = "README.md"
requires-python = ">=3.7"
classifiers = [
    "License :: OSI Approved :: MIT License",
    "Operating System :: OS Independent",
    "Programming Language :: Python :: 3",
]

dependencies = [
    "requests",
    "aiohttp",
    "numpy",
    "pandas",
    "openpyxl",
    "xlrd",
    "arrow",
    "pyyaml",
    "pypeln",
    "configobj",
    "datacompy",
    "dictdiffer",
    "jsonschema",
    "pymysql",
    "SQLAlchemy",
    "Faker",
    "ua-parser",
    "user_agents",
    "imgkit",
    "pycryptodome",
    "python-magic",
    "jira",
    "lxml",
    "sxtwl==1.1.0",
    "pika",
    "boto3",
    "paramiko",
]


[project.optional-dependencies]
hive = [
    "cython",
    "thriftpy",
    "bitarray==1.1.0",
    "impyla==0.16.2",
    "pure_sasl==0.6.2",
    "thrift_sasl==0.2.1",
]

test = [
    "pytest",
    "pytest-html",
    "pytest-sugar",
    "pytest-asyncio",
    "allure-pytest",
]


[project.urls]
"Homepage" = "https://baiyyee.github.io"
"Source" = "https://github.com/baiyyee/WeTest"
"Tracker" = "https://github.com/baiyyee/WeTest/issues"
```