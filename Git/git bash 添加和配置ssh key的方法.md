# git添加ssh key的方法

## 临时方法

此方法关闭bash后 失效

### 第1步

    eval `ssh-agent -s`
    ssh-add ssh密钥的文件名

### 第2步

    ssh git@github.com

如果显示欢迎界面则表示配置成功

    $ ssh git@github.com
    PTY allocation request failed on channel 0
    Hi onedou! You've successfully authenticated, but GitHub does not provide shell access.
    Connection to github.com closed.

## 保存文件方法

1. 如何生成ssh key
2. 如何使用特定ssh端口从git仓库拉取项目
3. 如何使用特定密钥文件从git仓库拉取项目

### 一、生成 ssh key
系统默认的ssh key存放在如下目录：

    [root@hostname ~]# cd ~/.ssh/
    [root@hostname .ssh]# ls
    authorized_keys  id_rsa  id_rsa.pub  known_hosts

我们将新建.Git目录，用来存放git相关部署key的公私钥。

    [root@hostname .ssh]# mkdir ~/.git
    [root@hostname .ssh]# ssh-keygen -t rsa -f ~/.git/pub_coding.id_rsa -C "sunsky.lau@gmail.com"
    Generating public/private rsa key pair.
    Enter passphrase (empty for no passphrase):  # 回车
    Enter same passphrase again:                 # 回车
    Your identification has been saved in /root/.git/pub_coding.id_rsa.
    Your public key has been saved in /root/.git/pub_coding.id_rsa.pub.
    The key fingerprint is:
    b1:30:9c:9c:24:78:54:1e:b1:bb:d9:65:3a:44:8c:3b sunsky.lau@gmail.com
    The key's randomart image is:
    +--[ RSA 2048]----+
    |   oo.=.         |
    |  . .* B         |
    |   .  @ +        |
    |       * o       |
    |      E S o      |
    |       * +       |
    |      o +        |
    |         .       |
    |                 |
    +-----------------+
    [root@hostname .ssh]# ls ~/.git/
    pub_coding.id_rsa  pub_coding.id_rsa.pub

通过上述操作，在~/.git目录下生成了2个文件，其中pub_coding.id_rsa为私钥，pub_coding.id_rsa.pub为公钥。

我们需要将公钥添加到相关版本控制软件的账户下。

### 二、使用特定ssh端口从git仓库拉取项目

这种情况一般会发生在我们本地的ssh默认端口和git仓库的ssh端口不一致时。比如，我们本地使用了57832作为ssh默认端口，而git仓库使用了22作为ssh默认端口。

这种情况，对使用https方式访问git仓库的用户是不会受到影响的，但是会导致使用ssh方式访问git仓库的用户拉取项目使用。

针对这个问题，这里提供两种解决方法：

使用ssh://的方式拉取项目

    [root@hostname .ssh]# git clone ssh://git@git.coding.net:端口号/用户名/项目名称.git

我们可以在上面的命令中去指明对应的ssh的端口号。  
使用ssh config配置来自定义端口
这种方式，我们将放到管理多ssh key的段落中去做介绍。

### 三、使用特定密钥文件从git仓库拉取项目

#### 1、介绍

这个问题，换句话说就是如何git如何使用多ssh key。针对这种多ssh key的管理，我们目前主要通过定义ssh的客户端配置文件来实现。

我们可以在ssh的客户端配置文件文件中定义服务器别名、服务器地址以及针对特定服务器使用的一些专用连接配置信息。

有关ssh的客户端配置文件，我们可以通过man config来获取相关的介绍，这里简单放一部分介绍。

    NAME
         ssh_config - OpenSSH SSH client configuration files

    SYNOPSIS
         ~/.ssh/config
         /etc/ssh/ssh_config

    DESCRIPTION
         ssh(1) obtains configuration data from the following sources in the following order:

               1.   command-line options
               2.   user’s configuration file (~/.ssh/config)
               3.   system-wide configuration file (/etc/ssh/ssh_config)

从描述中，我们可以看到，有关ssh的客户端配置文件有2个，分别是~/.ssh/config和/etc/ssh/ssh_config。他们一个是用户范围的配置，一个是系统范围的配置。
由于我们的操作要限定在用户范围，因此要使用~/.ssh/config文件。

#### 2、配置

需要注意的是，~/.ssh/config文件默认不存在，需要用户自己创建。
样例文件：

    [root@hostname ~]# touch ~/.ssh/config
    [root@hostname ~]# cat ~/.ssh/config
    # github key
    Host git-github
        Port 22
        User git
        HostName git.github.com
        PreferredAuthentications publickey
        IdentityFile ~/.git/pub_github.id_rsa

    # coding key
    Host git-coding
        Port 22
        User git
        HostName git.coding.net
        PreferredAuthentications publickey
        IdentityFile ~/.git/pub_coding.id_rsa

下面对上述配置文件中使用到的配置字段信息进行简单解释。

##### Host
它涵盖了下面一个段的配置，我们可以通过他来替代将要连接的服务器地址。
这里可以使用任意字段或通配符。

当ssh的时候如果服务器地址能匹配上这里Host指定的值，则Host下面指定的HostName将被作为最终的服务器地址使用，并且将使用该Host字段下面配置的所有自定义配置来覆盖默认的`/etc/ssh/ssh_config`配置信息。

##### Port
自定义的端口

##### User
自定义的用户名

##### HostName
真正连接的服务器地址

##### PreferredAuthentications
指定优先使用哪种方式验证，支持密码和秘钥验证方式

##### IdentityFile
指定本次连接使用的密钥文件  
通过上面设置之后，我们就可以使用多ssh key来连接不同的git仓库了

#### 3、连接测试
我们可以使用ssh来进行连接验证测试。

    [root@hostname .ssh]# ssh -T git@git-coding
    Hello 用户名 You've connected to Coding.net by SSH successfully!
    [root@hostname .ssh]# ssh -T git@git-github
    Hi 用户名! You've successfully authenticated, but GitHub does not provide shell access.

#### 4、拉取项目设置
通过上述设置之后，我们就可以通过不同的Host来针对不同的git仓库和git项目使用不同的ssh key了。但是，这里还需要注意的是，通常情况下我们从git仓库拉取的项目ssh访问地址，类似这种git@git仓库地址:用户名/项目名.git。我们一定要把这里的git仓库地址替换为我们ssh config里面设定的Host。

范例：

    [root@hostname .ssh]# git clone git@github.com:用户名/项目名.git

替换为如下

    [root@hostname .ssh]# git clone git@pub_github:用户名/项目名.git

到这里就大功告成了！
