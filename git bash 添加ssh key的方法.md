# git添加ssh key的方法

## 第1步

    eval `ssh-agent -s`
    ssh-add ssh密钥的文件名

## 第2步

    ssh git@github.com

如果显示欢迎界面则表示配置成功

    $ ssh git@github.com
    PTY allocation request failed on channel 0
    Hi onedou! You've successfully authenticated, but GitHub does not provide shell access.
    Connection to github.com closed.
