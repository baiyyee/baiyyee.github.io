# jenkins



### 安装
下载链接：https://www.jenkins.io/download/

安装方式：war/exe/docker

<br>

### 插件源
```
进入 Manage Jenkins -> Manage Plugin -> Advanced -> Update Site

https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json
http://mirror.xmission.com/jenkins/updates/update-center.json

详见：http://testingpai.com/article/1604671047314
```

<br>


### 推荐插件
- HTML Publisher plugin
- Allure
- Git
- Extended E-mail Notification


<br>

### pipeline
```
流水线语法：http://localhost:8000/job/pipeline/pipeline-syntax/
```

<br>

### Dockerfile
```Dockerfile
FROM jenkins/agent:alpine-jdk11
USER root
RUN apk add python3
RUN apk add py3-pip
USER jenkins
```

<br><br>

### 拓展阅读
- [Jenkins 101](https://github.com/devopsjourney1/jenkins-101)
- [Jenkins Doc](https://www.jenkins.io/doc/)
- [Learn Jenkins! Complete Jenkins Course](https://www.youtube.com/watch?v=6YZvp2GwT0A)