# 如何快速配置一个开发环境？


## 安装软件

1. 安装开发工具
   - VSCode
   - Git
   - WSL
   - Python

<br>


2. 配置开发工具

- 配置软件安装源
  - 备份默认安装源：`sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak`
  - 替换默认安装源：`sudo vim /etc/apt/sources.list`
    ```
    deb http://mirrors.aliyun.com/ubuntu/ jammy main restricted universe multiverse
    deb-src http://mirrors.aliyun.com/ubuntu/ jammy main restricted universe multiverse
    deb http://mirrors.aliyun.com/ubuntu/ jammy-updates main restricted universe multiverse
    deb-src http://mirrors.aliyun.com/ubuntu/ jammy-updates main restricted universe multiverse
    deb http://mirrors.aliyun.com/ubuntu/ jammy-security main restricted universe multiverse
    deb-src http://mirrors.aliyun.com/ubuntu/ jammy-security main restricted universe multiverse
    ```
  - 更新源：`sudo apt-get update`
  - 更新软件：`sudo apt-get upgrade`


- Git
  - 配置git用户名和邮箱
    ```shell
    git config --global user.name "username"
    git config --global user.email "useremail@test.com"
    ```
  - 生成SSH密钥：`ssh-keygen -t rsa -C "useremail@test.com"`
  - 拷贝 id_rsa.pub 内容: `cat ~/.ssh/id_rsa.pub`
  - 将 id_rsa.pub 内容添加到 GitHub


- Python
  - 安装pip3：
    ```shell
    sudo apt-get install python3-pip
    ```


  - 安装虚拟环境：
    ```shell
    sudo apt install virtualenv
    sudo apt install virtualenvwrapper
    ```


  - virtualenvwrapper 操作虚拟环境常用命令：
    ```shell
    mkvirtualenv <env_name>
    lsvirtualenv
    workon
    workon <env_name>
    rmvirtualenv <env_name>
    ```


- 终端
  - 安装终端：
    ```shell
    sudo apt-get install zsh
    sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
    ```

    
  - 安装插件：
    ```shell
    git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
    git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
    ```


  - 配置终端
    ```shell
    export ZSH="$HOME/.oh-my-zsh"

    ZSH_THEME="robbyrussell"

    plugins=(
            git
            zsh-autosuggestions
            zsh-syntax-highlighting
    )

    source $ZSH/oh-my-zsh.sh


    # Python Virtual Env
    export WORKON_HOME=~/.virtualenvs
    export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3
    export VIRTUALENVWRAPPER_VIRTUALENV=/usr/bin/virtualenv
    source /usr/share/virtualenvwrapper/virtualenvwrapper.sh

    source $ZSH_CUSTOM/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh
    source $ZSH_CUSTOM/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
    ```

