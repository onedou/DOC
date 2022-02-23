安装keychain命令
1. 到官网安装 https://www.funtoo.com
2. 使用brew安装 brew install keychian


将以下脚本加入.zshrc中

```
/usr/local/bin/keychain $HOME/.ssh/xxx
source $HOME/.keychain/xxxdembp-sh
```
