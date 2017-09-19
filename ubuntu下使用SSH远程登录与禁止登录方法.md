## 一、允许用户ssh远程登录

ubuntu默认是不启用root用户也不允许root远程登录的。所以需要先启用root用户  
启用root用户：sudo passwd root //修改密码后就启用了。

### 安装OpenSSH server：

#### 1. 使用apt命令安装openssh server

        $ sudo apt-get install openssh-server

#### 2. 可以对 openssh server进行配置

        $ sudo vi /etc/ssh/sshd_config

找到PermitRootLogin no一行，改为PermitRootLogin yes

#### 3. 重启 openssh server

        $ sudo service ssh restart

#### 4. 客户端如果是ubuntu的话，则已经安装好ssh client,可以用下面的命令连接远程服务器。

        $ ssh xxx.xxx.xxx.xxx

如果是windows系统的话，可以使用CRT等ssh软件进行远程连接。

## 二、Ubuntu下SSH远程连接

查看ssh是否启动成功
        
        netstat -tlp

最后运行命令

        ssh -l remote_username remote_serverip即可,如ssh -l test 192.168.1.222

## 三、限制ssh登录用户

禁止人家使用ssh端口登录就行了，具体方法：

        sudo vi /etc/ssh/sshd_config

查找 AllowUsers ，如果没有则加上。

        AllowUsers meiking root

上面表达的意思就是只允许 meiking和root用户远程登录
