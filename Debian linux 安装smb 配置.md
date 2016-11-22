# Debian linux 安装smb 配置

## 服务器端配置主要过程:

1. apt-get install samba
2. 修改/etc/samba/smb.conf文件
3. 添加用户并设置samba访问密码
  smbpasswd -a username
4. 重启一下samba服务：
  /etc/init.d/smb restart

## 服务器端配置详细过程:

1. apt-get install samba （安装）

   debconf （选择）

   workgroup（随便输入一个工作组，在windows网上邻居找到）

   ....

2. 创建用户 （ 注意：该用户是系统中已经存在的用户，以debian为例）

   创建passwd文件：touch /etc/samba/smbpasswd

   smbpasswd -a reny

   密码：

3. 编辑配置文件：

   vi /etc/samba/smb.conf

   加入我们的配置信息：

    [home]

    comment = laowang's data

    path = /home

    valid users = reny

    public = no

    writable = yes

    printable = no

    create mask = 0777

4. 重启一下samba服务：
/etc/init.d/samba restart
补充：# chmod 775 /home（window可以写）

## 服务器端配置详细过程2:

1.安装samba #apt-get install samba samba

> 注意：Samba 服务器的配置文件叫 smb.conf，位于 /etc/samba/目录下。在 /usr/share/samba/ 目录下也有一个 smb.conf 文件备份，如果你在配置服务器时把 /etc/samba/smb.conf 改乱了，就可以用该文件来恢复到初始状态。启动脚本位于 /etc/init.d/ 目录下，叫 samba，如果修改了 smb.conf 配置文件，可用 #/etc/init.d/samba restart 命令重启 Samba 服务器。/etc/default/samba 文件可设置 samba 服务器的启动方式，是 daemons 还是 inetd，默认的设置是采用daemons 方式的。

> 示例：实现windows 和debian的文件共享，在debian上建立一个共享文件夹，windows用户就可以修改这个共享文件夹。

2.建立用户

    #smb passwd -a 了linuxsir(给用户建立samba密码)

提示输入密码....

3.配置samba,打开配置文件/etc/samba/smb.conf,

替换为：

    [global]   --->全局配置，必写
    workgroup = LinuxSir ---〉Windows中显示的工作组
    netbios name = LinuxSir05 --->在Windows中显示出来的计算机名
    server string = Linux Samba Server TestServer --->Samba服务器说明
    security = share --->验证和登录方式,
    [linuxsir]
            path = /opt/linuxsir    --->共享目录的位置
            writeable = yes         ---〉可以向共享目录中写入
            browseable = yes         ---〉可以浏览
            guest ok = yes           ---〉匿名用户以guest身份登录

4.建立相应目录并授权

5.启动samba #/etc/init.d/samba start

6.检查当前配置 # testparm

7.假设windows下IP为192.168.0.7

  debian下的IP 为:192.168.0.8

在debian下输入：smbclient -L 192.168.0.7    访问windows

在windows下输入：\\192.168.0.8    访问debian ，在网上邻居就可以看到debian主机共享的文件夹了。

8.关闭服务器可用 smbcontrol 这个程序。命令格式如下：

    debian~:# smbcontrol smbd shutdown

附.设置目录共享及权限

    [share]                     设置共享名称
        comment =   目录的注解说明
        path = /data/temp     要共享目录的绝对位置
        以下属可选择项目录
        browseable =   no       目录是否可见,预设为可见
        writable = yes             目录是否为可写
        read only = no            目录是否为只读
        guest ok = yes            来宾是否可以访问,与"public = yes" 作用相同
        write list =user,@group   可写清单,@后表示某个群组
        valid users = ...            允许访问的使用者清单
        read list   =   ...               只可读的使用者清单
        invalid users = ...           禁止访问的使用者清单
        admin users = ...            有管理权限使用者清单
        create mask = 0755        使用者建立档案的权限,预设为0744

    #mkdir -p /opt/linuxsir
    #id nobody
    uid=65534(nobody) gid=65534(nogroup) groups=65534(nogroup)
    [root@localhost ~]# chown -R nobody:nogroup /opt/linuxsir

samba命令使用:

1.查看共享的目录

    #smbclient //debian

2.进入共享的目录操作

    #smbclient ＼＼＼＼debian＼＼share -U sunday

在提示符后输入“？”命令查找你可使用的命令。
