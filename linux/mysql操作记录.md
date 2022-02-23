## debian下完全删除mysql的方法

首先你可以通过dpkg --get-selections | grep mysql命令罗列出你电脑上安装的和MySQL相关的软件，然后purge卸载，我是这么做的：

复制代码 代码如下:

		sudo apt-get --purge remove mysql-server
		sudo apt-get --purge remove mysql-client
		sudo apt-get --purge remove mysql-common

最后再通过下面的命令清理残余：

复制代码 代码如下:

		apt-get autoremove
		apt-get autoclean
		rm /etc/mysql/ -R
		rm /var/lib/mysql/ -R

好了，至此卸载清理工作全部完成，下面可以重新安装了:-)

##DEBIAN 下 MYSQL 允许远程连接

虽然安全性可能会有一些问题，但用诸如 navicat 等工具来管理数据库，比 phpmyadmin 或者 adminer 要方便的多，所以需要将 mysql 配置为允许远程访问的形式。

1. 防火墙得开启 3306 端口

		vim /etc/iptables.conf

添加

		-A INPUT -p tcp –dport 3306 -j ACCEPT

重启

		iptables-restore < /etc/iptables.conf

2. 修改 mysql 的默认监听地址

		vim /etc/mysql/my.cnf

注释掉

		bind-address = 127.0.0.1

或者改为

		bind-address = 0.0.0.0

3. 修改root权限

		mysql -u root -p ‘yourpassword’

进入终端后输入

		GRANT ALL ON *.* TO ‘root’@’%’ IDENTIFIED BY ‘yourpassword’;
		exit;

4. 重启

		/etc/init.d/mysql restart

一般情形经过上述四步就可以了，但偏偏我的仍然不行，在第三步中设置权限时总是提示：“Access denied for user ‘root’@’localhost’ (using password: YES)”，原因在于 debian 系统下 mysql 安装时设置的密码和我当前的root密码不一致导致的，安装时的密码被配置在了 /etc/mysql/debian.cnf 中，应该使用这个配置来登录 mysql 终端，然后再改掉 root 密码。

		mysql -u debian-sys-maint -p ‘password in debian.cnf’

进入后：

		use mysql;
		update user set password=PASSWORD(‘your new root password’) where user=’root’;
		FLUSH PRIVILEGES;

这时重复第三步应该就没有问题了。远程使用 navicat 连接也正常了。

## 忘记Mysql密码

如果忘记了MySQL root密码，可以用以下方法重新设置：

1. KILL掉系统里的MySQL进程

    lsof -i -P | grep LISTEN
    #查看mysql的进程号 并kill 进程号

2. 用以下命令启动MySQL，以不检查权限的方式启动

    mysql_safe --skip-grant-tables &

3. 然后用空密码方式使用root用户登录 MySQL

    mysql -u root

4. 修改root用户的密码
	MySQL> use mysql;
    MySQL> update user set password=PASSWORD('新密码') where User='root';  
    MySQL> flush privileges;  
    MySQL> quit;
