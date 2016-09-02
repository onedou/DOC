#nginx环境中php-fpm php错误提示开启方法

通常情况下编辑php配置文件：php.ini

	error_reporting = E_ERROR
	display_errors = On

编辑php配置文件：

	vi /etc/php.ini
	error_reporting = E_ERROR
	display_errors = On

因为我开启了php-fpm。  
所以，还要编辑 php-fpm.conf文件，把php_flag[display_errors]设为on： 

	vi php-fpm.conf
	php_flag[display_errors] = on

这样在开发的时候就可以在浏览器中显示php出现的错误了，友情提示如果你未安装php-fpm那么我们就不需要向下一步操作。