# ~/.zshrc


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
export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3.8
export VIRTUALENVWRAPPER_VIRTUALENV=~/.local/bin/virtualenv
source ~/.local/bin/virtualenvwrapper.sh

source $ZSH_CUSTOM/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh
source $ZSH_CUSTOM/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh

# Set Alias
alias python=/usr/bin/python3.8
alias pip=/usr/bin/pip3


# Env Variable
export ROBOT_KEY={{WECHAT_ROBOT_KEY}}

# Deta Token
export DETA_ACCESS_TOKEN={{DETA_ACCESS_TOKEN}}

export PATH="/home/hhb/.deta/bin:$PATH"
```