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
