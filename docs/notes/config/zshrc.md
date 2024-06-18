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
export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3
export VIRTUALENVWRAPPER_VIRTUALENV=/usr/share/virtualenvwrapper/virtualenvwrapper.sh
source /usr/share/virtualenvwrapper/virtualenvwrapper.sh

source $ZSH_CUSTOM/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh
source $ZSH_CUSTOM/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
```