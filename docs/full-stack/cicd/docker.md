# docker


```
https://www.docker.com/
https://www.docker.org.cn/
https://www.bilibili.com/video/BV1Vs411E7AR?from=search&seid=7596747318335787395

开发 =》 运维：系统&环境&配置不一致，导致软件不能按预期运行

Docker: 代码/配置/系统/数据/... 整体环境打包  =》 软件带环境部署，避免在开发机器上正常工作，一部署到服务器上就各种问题


image: 

在集群部署场景中，会包含很多机器，在一台机器上装完环境后，需要一遍又一遍重复安装到其它机器，过程繁琐，但如果把其中一台机器的环境做成镜像，在分发到其它所有机器中，这样就很好地解决了集群部署过程中的繁琐过程

一次封装（构建），处处运行

docker三要素：仓库、镜像、容器


虚拟机：占用资源多、冗余步骤多、启动慢


LXC： Linux Containers


容器 =》 集装箱：没有自己的内核，没有硬件虚拟，彼此隔离
鲸鱼 =》 Docker


devops => 开发自运维


轻量：centos/ubuntu 基础镜像仅170M，仅包含业务运行时所需的runtime
高效：无操作系统虚拟化开销
敏捷，灵活
秒级启动


凡技术，必登官网


docker hub：镜像仓库 => github


lsb_release -a          
    No LSB modules are available.
    Distributor ID: Ubuntu
    Description:    Ubuntu 20.04 LTS
    Release:        20.04
    Codename:       focal


docker build
docker pull 
docker run

镜像：只读模板 =》 一个镜像可以创建多个容器
容器：镜像的实例  
    =》 Persion p1 = new Persion()
    =》 Persion p2 = new Persion()
    =》 Persion p3 = new Persion()

    Persion =》 镜像；p1,p2,p3 => 容器

    容器可以被启动、开始、停止、删除，每个容器相互隔离，每个容器就是一个集装箱，可以把容器看作是一个简易版的Linux环境（Linux很多命令可以在docker内使用）


仓库：集中存放镜像的场所


tag: 版本号


docker本身是一个容器运行载体或称之为管理引擎。我们把应用程序和配置依赖打包成一个可交付的运行环境，这个打包好的运行环境就是镜像文件（image），只有通过这个镜像文件才能生成docker容器，image文件可以看作是容器的模板。
docker根据image文件生成容器的实例，同一个image文件，可以生成多个同时运行的实例。


一个容器运行一种服务（如 nginx/redis/mysql等）




centos 6.8:
yum install -y epel-release 
yum install -y docker-io
/etc/sysconfig/docker => 配置文件地址




service docker start
windows docker desktop 手动重启



https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors

sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://xxxxxx.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker


ps -ef | grep docker


docker run hello-world   (hello-world 为官方测试镜像)

===============================================================================================






大海   - 宿主机
鲸鱼   - docker
集装箱 - 容器实例（来自image）



docker images   => 列出本地镜像列表
    REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
    hello-world   latest    bf756fb1ae65   11 months ago   13.3kB

    REPOSITORY: 镜像仓库源
    TAG: 镜像的标签
    IMAGE ID: 镜像ID
    CREATED: 镜像创建时间
    SIZE: 镜像大小

    同一个仓库源可以有多个TAG，代表这个仓库源的不同版本，我们使用 REPOSITORY:TAG 来定义不同的镜像。如果不指定一个镜像的版本标签，默认使用 latest 标签。


docker version                    => 查看docker版本
docker info                       => docker 信息

docker --help  images             => 查看 images 命令帮助信息

docker images -a                  => a: all, 列出本地所有镜像（包括中间映像层，镜像是分层的千层饼）
docker images -q                  => 仅显示镜像ID
docker images -aq                 => 列出本地所有镜像的镜像ID
docker images --digests           => 显示镜像的摘要信息
docker images --no-trunc          => 不截取，显示完整的镜像信息
docker images nginx --digests     => 显示某具体镜像的摘要信息

docker search nginx                                             => 从 docker hub (https://hub.docker.com) 上搜索镜像（NOTE： 查询地址和下载地址不一致，查询是从https://hub.docker.com查询，下载是从我们配的镜像地址下载 ）
docker search nginx --no-trunc                                  => 不截取，显示完整的镜像信息
docker search nginx --limit 3                                   => 限制查询结果的最大返回个数
docker search nginx --filter stars=3 --filter is-official=true  => 搜索stars大于等于3且来源为官方版本的nginx镜像

    ➜  ~ docker search tomcat           
        NAME                          DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
        tomcat                        Apache Tomcat is an open source implementati…   2894      [OK]       
        tomee                         Apache TomEE is an all-Apache Java EE certif…   85        [OK]       
        dordoka/tomcat                Ubuntu 14.04, Oracle JDK 8 and Tomcat 8 base…   55                   [OK]

        dordoka/tomcat => dordoka 为命名空间



docker pull nginx                                               => 下载 nginx 镜像（未指定tag时默认下载最新版本，默认追加 latest TAG）

docker rmi nginx                                                => remove image, 删除镜像
docker rmi nginx -f                                             => 强制删除镜像，忽略该镜像被容器引用的场景
docker rmi nginx hello-world -f                                 => 强制删除多个镜像
docker rmi $(docker images -aq) -f                              => 强制删除所有镜像


docker run
    => -i: 以交互模式运行容器，通常与 -t 同时使用
    => -t: 为容器重新分配一个伪输入终端，通常与 -i 同时使用
    => -d: 后台运行容器，并返回容器ID，也即启动守护式容器
        NOTE: docker容器后台运行必须要有一个前台进程（容器运行的命令如果不是那些一直挂起的命令如 top/tail，就是会自动退出的）
    -p: 主机端口:docker容器端口
    -P: 随机分配端口
    => 运行容器时若未指定容器名称则docker自动随机生成一个容器名称

    docker run -it nginx
    docker run -d nginx
    docker run -it ubuntu  <=> 等价于  docker run -it ubuntu /bin/bash  (默认以 /bin/bash 运行)
    docker run -it -p 8888:8080 tomcat  => 指定端口 => http://localhost:8888
    docker run -it -P tomcat            => 随机端口 => 0.0.0.0:32768->8080/tcp => http://localhost:32768

    docker run -it --env-file tests/haa/config/env/localenv 4ada7665647c /bin/bash  => 传递环境变量到容器


docker run --name hhbcreate hello-world

    NOTE: docker 命令顺序错误可能会导致以下异常产生，如下：
        (haa_test) ➜  haa-test git:(hhb) ✗ docker run hello-world --name hhbcreate
        docker: Error response from daemon: OCI runtime create failed: container_linux.go:370: starting container process caused: exec: "--name": executable file not found in $PATH: unknown.
        ERRO[0000] error waiting for container: context canceled 




docker ps                   => 查看当前运行的容器信息
docker ps -q                => 静默模式，仅列出当前运行的容器ID
docker ps -a                => 查看所有的容器信息（包括当前运行的和历史运行过的容器）
docker ps -l                => l: last, 列出上一次运行的容器信息
docker ps -n 5              => 列出上5次运行的容器信息

docker start e4c3013031f4   => 启动容器
docker stop e4c3013031f4    => 停止容器（开始 -> 电源 -> 关机）
docker kill e4c3013031f4    => 强制停止容器（拔电源）
docker restart e4c3013031f4 => 重启容器

docker rm e4c3013031f4      => 删除已停止的容器（若容器正在运行，可能提示删除冲突）
docker rm e4c3013031f4 -f   => 强制删除容器（可删除正在运行的容器）


docker rm $(docker ps -aq)      => 方式1：删除所有容器
docker ps -aq | xargs docker rm => 方式2：删除所有容器


退出容器：
    exit         => 关闭容器并退出
    Ctrl + P + Q => 容器不停止退出
        

docker attach f67589c50f57                        => 重新进入还在运行的容器,不会启动新的进程

docker exec -t 84013b0e931c ls -l /tmp            => 在宿主系统上（不进入到容器内）执行某已存在容器内的命令
docker exec -t 84013b0e931c /bin/bash             => 进入容器并执行/bin/bash命令


docker top f67589c50f57                           => 查看容器内运行的进程信息

docker inspect f67589c50f57                       => 查看容器内部细节

docker cp 84013b0e931c:/tmp/hhb.txt .             => 将容器内文件 /tmp/hhb.txt COPY到宿主机器当前目录下




docker logs
    -f: 跟随最新的日志打印
    -t: 时间戳
    --tail n: 显示最后 n 条

    docker run -d ubuntu /bin/bash -c "while true; do echo hello hhb; sleep 5; done"           # 人为设置一个持续运行的进程
    
    docker logs f67589c50f57                      => 打印某容器内的全部日志信息
    docker logs -f f67589c50f57                   => 追加打印某容器内的日志信息
    docker logs -t f67589c50f57                   => 打印某容器内的日志信息和日志对应的时间戳
    docker logs --tail 3 f67589c50f57             => 打印某容器内最新3条日志信息
    docker logs -ft --tail 3 f67589c50f57         => 命令的组合运用





镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需的所有内容，包括代码、运行时、库，环境变量和配置文件。

UnionFS (联合文件系统): Union文件系统（UnionFS）是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同的目录挂载到同一个虚拟文件系统下。
Union文件系统是Docker镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。

特性：一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有底层的文件和目录。

Docker镜像都是只读的，当容器启动时，一个新的可写层被加载到镜像的顶部，这一层通常被称作”容器层“，”容器层“之下的都叫”镜像层“。

docker镜像加载原理：

    docker 镜像实际上由一层一层的文件系统组成，这种层级的文件系统UnionFS.


    kernal -> centos -> jdk8 -> tomcat  => 分层的镜像

    镜像分层的好处：共享资源
    比如：有多个镜像都是从相同的base镜像构建而来，那么宿主机只需在磁盘上保存一份base镜像，同时内存中也只需要加载一份base镜像，就可以为所有容器服务了。而且镜像的每一层都可以被共享。


docker commit => 提交对当前容器的修改使之成为一个新的镜像
    docker commit -m="提交的描述信息" -a="作者" 容器ID 目标镜像名称:TAG名称   => docker commit 命令格式

    Scenario:
        1. docker run -it -p 8888:8080 tomcat
        2. cd /usr/local/tomcat/weapps; rm -rf docs
        3. docker commit

        docker commit -a="hhb" -m="tomcat without docs" f67589c50f57 hhb/tomcat_new:2.0  (f67589c50f57为当前正在运行且已被修改的容器)

        docker run -it -p 7777:8080 hhb/tomcat_new:2.0



docker 容器数据卷：
    - 容器的持久化
    - 容器间继承 + 数据共享


docker理念：
    - 将应用与运行环境打包形成容器运行，运行可以伴随着容器，但是我们对数据的要求希望时持久化的
    - 容器之间希望有可能共享数据

docker 容器产生的数据，如果不通过 docker commit 生成新的镜像，使得数据成为镜像的一部分保存下来，那么当容器被删除后，数据自然也就没有了。为了保存docker中的数据，我们使用卷。

卷 就是目录或文件，存在于一个或多个容器中，由docker挂载到容器，但不属于联合文件系统，因此能绕过Union File System提供的一些用于持续存储或共享数据的特性：
卷 设计的目的就是数据的持久化，完全独立于容器的生命周期，因此docker不会在容器删除时删除其挂载的数据卷

特点：
    - 数据卷可在容器之间共享或重用数据
    - 卷中的更改可以直接生效
    - 数据卷中的更改不会包含在镜像的更新中
    - 数据卷的生命周期一直持续到没有容器使用它为止
    - 数据卷可以实现容器到宿主机或宿主机到容器之间的数据共享

NOTE: 容器被停止后，对主机数据卷的更新同样会同步到容器中，当容器被重新启动时，更新生效


命令行方式添加数据卷：
    docker run -it -v /宿主机目录:/容器内目录:权限配置 镜像名
    docker run -it -v /home/hhb/hhbdata:/home/hhb/hhbvolume ubuntu   (目录不存在时会自动创建，未指定读写权限时默认有读写权限)


        docker inspect ac96d974ee14  => 验证数据卷是否绑定成功

            "HostConfig": {
                "Binds": [
                    "/home/hhb/hhbdata:/home/hhb/hhbvolume"
                ],
                "ContainerIDFile": "",
                "LogConfig": {
                    "Type": "json-file",
                    "Config": {}
                },
            }

            "Mounts": [
                {
                    "Type": "bind",
                    "Source": "/home/hhb/hhbdata",
                    "Destination": "/home/hhb/hhbvolume",
                    "Mode": "",
                    "RW": true,
                    "Propagation": "rprivate"
                }
            ],

    docker run -it -v /home/hhb/hhbdata:/home/hhb/hhbvolume:ro ubuntu  => 设置容器内只读权限(ro)，容器内不可更新编辑数据卷内容，宿主机对数据卷的更新操作不受影响

            "Mounts": [
                {
                    "Type": "bind",
                    "Source": "/home/hhb/hhbdata",
                    "Destination": "/home/hhb/hhbvolume",
                    "Mode": "ro",
                    "RW": false,
                    "Propagation": "rprivate"
                }
            ],

        root@2680dc0d078e:/home/hhb/hhbvolume# touch ro.txt
        touch: cannot touch 'ro.txt': Read-only file system

    NOTE: docker 挂载宿主机目录访问出现 cannot open directory .: Permissionn denied  => 解决办法：在挂载目录后多加一个 --privileged=true 参数
    docker run -it -v /home/hhb/hhbdata:/home/hhb/hhbvolume --privileged=true ubuntu


通过DockerFile方式添加卷：
    VOLUME ["/dataVolumeContainer", "/dataVolumeContainer2", "/dataVolumeContainer3"]

    说明：
        处于可移植和分享的考虑，用 -v 宿主机目录:容器内目录 这种方法不能直接在DockerFile 中实现
        由于宿主机目录是依赖于特定宿主机的，并不能保证在所有的宿主机上都存在这样的特定目录。


    DockerFile:

    vim /home/hhb/DockerFile
        FROM ubuntu
        VOLUME ["/dataVolumeContainer", "/dataVolumeContainer2"]
        CMD echo "finished, successed"
        CMD /bin/bash


    docker build -f /home/hhb/DockerFile -t hhb/ubuntu .

        ➜  ~ docker build -f DockerFile -t hhb/ubuntu:custom_1.0 .
        [+] Building 0.4s (5/5) FINISHED                                                                                                            
        => [internal] load build definition from DockerFile                                                                                   0.1s
        => => transferring dockerfile: 157B                                                                                                   0.0s
        => [internal] load .dockerignore                                                                                                      0.1s
        => => transferring context: 2B                                                                                                        0.0s
        => [internal] load metadata for docker.io/library/ubuntu:latest                                                                       0.0s
        => [1/1] FROM docker.io/library/ubuntu                                                                                                0.1s
        => => resolve docker.io/library/ubuntu:latest                                                                                         0.0s
        => exporting to image                                                                                                                 0.0s
        => => exporting layers                                                                                                                0.0s
        => => writing image sha256:466c05608a0a3bb9fa1703b9c52372af63380956206e155a2a0c6cdaa9b7c9a1                                           0.0s
        => => naming to docker.io/hhb/ubuntu:custom_1.0                                                                                       0.0s



        NOTE：DockerFile 中虽未映射宿主机的数据卷，执行 docker build 命令后，会自动将容器中的数据卷映射到主机数据卷

            "Mounts": [
                {
                    "Type": "volume",
                    "Name": "1c0399eab660f590fe60994c35077d61b74a36f0856a08059f1ee4073166fcb9",
                    "Source": "/var/lib/docker/volumes/1c0399eab660f590fe60994c35077d61b74a36f0856a08059f1ee4073166fcb9/_data",
                    "Destination": "/dataVolumeContainer2",
                    "Driver": "local",
                    "Mode": "",
                    "RW": true,
                    "Propagation": ""
                },
                {
                    "Type": "volume",
                    "Name": "a0da56cd9ce7bbbbdd8f86efc321a74a454c202e57dec19555cea0de09baa6c4",
                    "Source": "/var/lib/docker/volumes/a0da56cd9ce7bbbbdd8f86efc321a74a454c202e57dec19555cea0de09baa6c4/_data",
                    "Destination": "/dataVolumeContainer",
                    "Driver": "local",
                    "Mode": "",
                    "RW": true,
                    "Propagation": ""
                }
            ]



数据卷容器：命名的容器挂载数据卷，其它容器通过挂载这个（父容器）实现数据共享，挂载数据卷的容器，称之为数据卷容器

docker run -it --name dc01 hhb/ubuntu:custom_1.0

dc02,dc03 继承 dc01 => 

    docker run -it --name dc02 --volumes-from dc01 hhb/ubuntu:custom_1.0
    docker run -it --name dc02 --volumes-from dc03 hhb/ubuntu:custom_1.0


NOTE：子级和父级的任何更新都同步给彼此，即使后面父级容器被删除了，数据共享仍然不受影响 => 容器之间配置信息的传递，数据卷的生命周期一直持续到没有容器使用它为止


DockerFile => 用来构建docker镜像的构建文件，是由一系列命令和参数构成的脚本

1. Dockerfile 
2. docker build
3. docker run


Dockerfile 语法基础：
    - 每条保留字指令都必须为大写字母且后面至少包含一个参数
    - 指令从上到下依次执行
    - # 表示注释
    - 每条指令都会创建一个新的镜像层，并对镜像进行提交


Docker执行Dockerfile的大致流程：
    1. docker 从基础镜像运行一个容器
    2. 执行一条指令并对容器作出修改
    3. 执行类似 docker commit 的操作提交一个新的镜像层
    4. docker 再基于刚提交的镜像运行一个新容器
    5. 执行dockerfile 中的下一条指令只到所有指令都执行完成



Dockerfile 保留字指令：
    FROM:       基础镜像，表明当前镜像是基于哪个基础镜像的
    MAINTAINER: 镜像维护者信息，姓名和邮箱
    RUN:        容器构建时需要运行的命令
    EXPOSE:     对外暴露的端口
    WORKDIR:    指定在创建容器后，终端默认登录进来的工作目录（未指定时，默认为根目录）
    ENV:        用来在构建镜像过程中设置环境变量（环境变量可以在后续的任何RUN指令中使用，也可以在其它指令中使用这些环境变量，如： ENV MY_PATH /usr/mytest  => WORKDIR $MY_PATH）
    ADD:        将宿主机目录下的文件拷贝进镜像且ADD命令会自动处理URL和解压tar压缩包
    COPY:       类似ADD，拷贝文件和目录到镜像中（将从构建上下文目录中 <源路径>的 文件/目录 复制到新的一层的镜像内的 <目标路径> 位置）
    VOLUME:     容器数据卷，用于数据保存和持久化工作
    CMD:        指定一个容器启动时要运行的命令（Dockerfile 中可以指定多个CMD命令，但只有最后一个生效，CMD 会被 docker run 之后的参数替换）
    ENTRYPOINT: 指定一个容器启动时要运行的命令（ENTRYPOINT的目的和CMD一样，都是在指定容器启动程序及参数，docker run 之后的参数会追加到ENTRYPOINT之后而不是替换）
    ONBUILD:    当构建一个被继承的Dockerfile时运行命令，父镜像在被子镜像继承后父镜像的onbuild被触发




场景1：自定义myubuntu，要求如下：
    - 重新设置登录后的默认路径
    - vim编辑器
    - 支持 ifconfig 命令

    Dockerfile_01

        FROM centos
        MAINTAINER huabo<hhbstar@hotmail.com>
        ENV MYPATH /tmp
        WORKDIR $MYPATH

        RUN yum -y install vim-nox
        RUN yum -y install net-tools

        EXPOSE 80

        CMD echo $MY_PATH
        CMD echo "build success"

        CMD /bin/bash



docker history  => 列出镜像的变更历史

    ➜  ~ docker history a61170b45d0f
    IMAGE          CREATED          CREATED BY                                      SIZE      COMMENT
    a61170b45d0f   11 minutes ago   CMD ["/bin/sh" "-c" "/bin/bash"]                0B        buildkit.dockerfile.v0
    <missing>      11 minutes ago   CMD ["/bin/sh" "-c" "echo \"build success\""]   0B        buildkit.dockerfile.v0
    <missing>      11 minutes ago   CMD ["/bin/sh" "-c" "echo $MY_PATH"]            0B        buildkit.dockerfile.v0
    <missing>      11 minutes ago   EXPOSE map[80/tcp:{}]                           0B        buildkit.dockerfile.v0
    <missing>      11 minutes ago   WORKDIR /tmp                                    0B        buildkit.dockerfile.v0
    <missing>      11 minutes ago   ENV MYPATH=/tmp                                 0B        buildkit.dockerfile.v0
    <missing>      11 minutes ago   MAINTAINER huabo<hhbstar@hotmail.com>           0B        buildkit.dockerfile.v0
    <missing>      2 weeks ago      /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B        
    <missing>      2 weeks ago      /bin/sh -c mkdir -p /run/systemd && echo 'do…   7B        
    <missing>      2 weeks ago      /bin/sh -c [ -z "$(apt-get indextargets)" ]     0B        
    <missing>      2 weeks ago      /bin/sh -c set -xe   && echo '#!/bin/sh' > /…   811B      
    <missing>      2 weeks ago      /bin/sh -c #(nop) ADD file:4f15c4475fbafb3fe…   72.9MB


场景2：CMD VS ENTRYPOINT => 都是指定一个容器启动时要运行的命令(CMD 覆盖、ENTRYPOINT追加)

    Dockerfile_02

        FROM centos
        RUN yum install -y curl
        CMD ["curl", "-s", "http://ip.cn"]

        => docker run a61170b45d0f -i
            => System Error: exec "-i"

        
        ==> 

        FROM centos
        RUN yum install -y curl
        ENTRYPOINT ["curl", "-s", "http://ip.cn"]


场景3： ONBUILD (父镜像中包含了ONBUILD时，子镜像构建时会自动触发父镜像ONBUILD对应的命令)

    DockerFile_father:

        FROM centos
        RUN yum install -y curl
        ENTRYPOINT ["curl", "-s", "http://ip.cn"]
        ONBUILD RUN echo "father images onbuild" 

    
    docker build -f DockerFile_father -t DockerFile_father .

    DockerFile_child:

        FROM DockerFile_father
        RUN yum install -y curl
        ENTRYPOINT ["curl", "-s", "http://ip.cn"]
        ONBUILD RUN echo "father images onbuild" 

    docker build -f DockerFile_father -t DockerFile_child .


场景4： COPY & ADD (COPY 复制；ADD 复制并解压缩)

    1. mkdir -p /home/hhb/hhbdata
    2. touch copy.txt
    3. cp /opt/apache-tomcat-9.0.8.tar.gz .;cp /opt/jdk-8u171-linux-x64.tar.gz .
    
    4. 在/home/hhb/hhbdata新建Dockerfile 文件
    5. build
    6. run
    7. 验证
    8. release

    ls /home/hhb/hhbdata
    copy.txt
    /opt/apache-tomcat-9.0.8.tar.gz

    Dockerfile_03:
        FROM centos
        MAINTAINER hhb<hhb@hotmail.com>

        #把宿主机当前上下文的 copy.txt 拷贝到容器 /usr/local/ 路径下
        COPY copy.txt /usr/local/copy_in_container.txt

        #把Tomcat添加到容器中
        ADD jdk-8u171-linux-x64.tar.gz /usr/local/
        ADD apache-tomcat-9.0.8.tar.gz /usr/local/

        #安装vim
        RUN yum -y install vim

        #设置工作访问时的WORKDIR路径，登录落脚点
        ENV MY_PATH /usr/local/
        WORKDIR $MY_PATH

        #配置Java和Tomcat环境变量
        ENV JAVA_HOME /usr/local/jdk1.8.0_171
        ENV CLASSPATH $JAVA_HOME/lib/dt.jar;$JAVA_HOME/lib/tools.jar
        ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.8
        ENV CATALINA_BASE /usr/local/apache-tomcat-9.0.8
        ENV PATH $PATH:$JAVA_HOME/bin;$CATALINA_HOME/lib;$CATALINA_HOME/bin

        #容器运行时监听的端口
        EXPOSE 80

        # 启动时运行 tomcat
        #ENTRYPOINT ["/usr/local/apache-tomcat-9.0.8/bin/startup.sh"]
        #CMD ["/usr/local/apache-tomcat-9.0.8/bin/catlina.sh", "run"]
        CMD /usr/local/apache-tomcat-9.0.8/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.8/bin/logs/catalina.out



docker run Demo:

    docker run -d -p 8090:8080 --name mytomcat9
    -v /home/hhb/hhbdata/test:/usr/local/apache-tomcat-9.0.8/webapps/test
    -v /home/hhb/hhbdata/tomcat9logs:/usr/local/apache-tomcat-9.0.8/webapps/logs
    --privileged=true
    hhbtomcat9



    docker run -p 12345:3306 --name mysql 
    -v /home/hhb/mysql/conf:/etc/mysql/conf.d 
    -v /home/hhb/mysql/logs:/usr/local/logs
    -v /home/hhb/mysql/data:/var/lib/mysql 
    -e MYSQL_ROOT_PASSWORD=123456 
    -d mysql:latest


    docker exec  a61170b45d0f /bin/bash
    mysql -uroot -p

    #备份dockers容器数据库数据到宿主机
    dokcer exec a61170b45d0f sh -c 'exec mysqldump --all-databases -uroot -p"123456"' > /home/hhb/mysql/data/alll-databases.sql



    docker pull redis 

    docker run -p 6379:6379 
    -v /home/hhb/redis/data:/usr/local/data
    -v /home/hhb/redis/conf/redis.conf:/usr/local/etc/redis/redis.conf
    -d redis:latest redis-server /usr/local/etc/redis/redis.conf
    --appendonly yes

    NOTE: 
        - /home/hhb/redis/conf/redis.conf 为文件夹
        - --appendonly yes 配置持久化

    设置 /home/hhb/redis/conf/redis.conf/redis.conf

    docker exec -it a61170b45d0f redis-cli




docker push:

    docker commit -a="hhb" -m="update" a61170b45d0f mycentos:1.1
    docker tag a61170b45d0f registry.cn-hangzhou.aliyuncs.com/hehuabo/mycentos:1.1
    docker push registry.cn-hangzhou.aliyuncs.com/hehuabo/mycentos:1.1

    docker pull registry.cn-hangzhou.aliyuncs.com/hehuabo/mycentos:1.1  (公网下载镜像)

```