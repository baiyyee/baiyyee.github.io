# ~/.pip/pip.conf


### 设置 pip 源
```
[global]
index-url = https://mirrors.aliyun.com/pypi/simple/
extra-index-url = https://pypi.tuna.tsinghua.edu.cn/simple/, https://pypi.python.org/simple
```

<br>

### 常用命令
```
pip install WeTest
pip install "WeTest[hive]"
pip install -r requirements.txt
pip install -e /mnt/d/src/python/WeTest
```