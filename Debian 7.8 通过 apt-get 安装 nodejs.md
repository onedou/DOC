#Debian 7.8 通过 apt-get 安装 nodejs

##查看当前系统版本

	$ cat /etc/debian_version 
	7.8
	$

##安装 curl和源

	$ sudo apt-get install curl
	$ sudo curl -sL https://deb.nodesource.com/setup | bash -

##安装 nodejs

	$ apt-get install -y nodejs