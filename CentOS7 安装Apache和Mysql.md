# CentOS7 安装Apache和Mysql 

## Apache在CentOS中的名称为httpd
	sudo yum intsall httpd

## CentoOS7 只能通过mariadb安装mysqlDB
	sudo yum install mariadb

## 启动mariadb服务
	service mariadb start

## 安全设置

centos apache PHP mkdir: Permission denied problem
centos 虚拟机上遇到这个文件，一个最基本的mkdir命令竟然无法执行，owner设置了Apache完全权限也不行。 

	chcon -R -t httpd_sys_content_t /path/to/www
	chcon -R -t httpd_sys_rw_content_t /path/to/www/dir/for/rw

