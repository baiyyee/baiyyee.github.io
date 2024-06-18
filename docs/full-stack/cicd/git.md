# git


### 安装

```console
sudo apt-get update
git --version
```


### 配置用户名和邮箱
```console
git config --global user.name "username"
git config --global user.email "useremail@test.com"
```

### 生成密钥
```console
ssh-keygen -t rsa -C "useremail@test.com"
cat ~/.ssh/id_rsa.pub
```

<br>


### Git Hooks

<br>

### 常用指令

```console
git checkout -b dev
git push origin dev
```