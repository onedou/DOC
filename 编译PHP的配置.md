## 创建WWW用户

		groupadd www
		useradd -g www -s /sbin/nologin -M www

## 编译安装

		./configure \
		--prefix=/etc/php5 \
		--with-config-file-path=/etc/php5/etc \
		--enable-inline-optimization \
		--disable-debug \
		--disable-rpath \
		--enable-shared \
		--enable-opcache \
		--enable-fpm \
		--with-fpm-user=www \
		--with-fpm-group=www \
		--with-mysql=mysqlnd \
		--with-mysqli=mysqlnd \
		--with-pdo-mysql=mysqlnd \
		--with-gettext \
		--enable-mbstring \
		--with-iconv \
		--with-mcrypt \
		--with-mhash \
		--with-openssl \
		--enable-bcmath \
		--enable-soap \
		--with-libxml-dir \
		--enable-pcntl \
		--enable-shmop \
		--enable-sysvmsg \
		--enable-sysvsem \
		--enable-sysvshm \
		--enable-sockets \
		--with-curl \
		--with-zlib \
		--enable-zip \
		--with-bz2 \
		--with-readline \
		--disable-fileinfo

## 开始编译

		make //多核时可以启用 -j8
		make install

## 出现问题时用到的命令
		sudo apt-get install libxml2-dev
		sudo apt-get install libbz2-dev
		sudo apt-get install libmcrypt-dev
		sudo apt-get install libreadline-dev
		sudo apt-get install libcurl4-openssl-dev pkg-config

--disable-fileinfo 服务器内存只有1G时可以启用

## 配置PHP
配置文件：

		cp php.ini-development /etc/php5/etc/php.ini
		//复制php.ini 到php程序目录

> 开启 php-fpm 服务

		cp /etc/php5/etc/php-fpm.conf.default /etc/php5/etc/php-fpm.conf

		//进入php项目工程目录
		cp sapi/fpm/init.d.php-fpm /etc/init.d/php-fpm5
		chmod +x /etc/init.d/php-fpm5

> 启动 php-fpm

		service php-fpm5 start
		//或 /etc/init.d/php-fpm5 start
		//php-fpm 可用参数 start|stop|force-quit|restart|reload|status

> 将PHP加入环境变量
		
		ln -s /etc/php5/bin/php /usr/sbin/php

> 配置 nginx
		
		http://www.ilanni.com/?p=7609

## 编译成功后的信息

		Installing shared extensions:     /etc/php5/lib/php/extensions/no-debug-non-zts-20131226/
		Installing PHP CLI binary:        /etc/php5/bin/
		Installing PHP CLI man page:      /etc/php5/php/man/man1/
		Installing PHP FPM binary:        /etc/php5/sbin/
		Installing PHP FPM config:        /etc/php5/etc/
		Installing PHP FPM man page:      /etc/php5/php/man/man8/
		Installing PHP FPM status page:   /etc/php5/php/php/fpm/
		Installing PHP CGI binary:        /etc/php5/bin/
		Installing PHP CGI man page:      /etc/php5/php/man/man1/
		Installing build environment:     /etc/php5/lib/php/build/
		Installing header files:           /etc/php5/include/php/
		Installing helper programs:       /etc/php5/bin/
		  program: phpize
		  program: php-config
		Installing man pages:             /etc/php5/php/man/man1/
		  page: phpize.1
		  page: php-config.1
		Installing PEAR environment:      /etc/php5/lib/php/
		[PEAR] Archive_Tar    - installed: 1.4.0
		[PEAR] Console_Getopt - installed: 1.4.1
		[PEAR] Structures_Graph- installed: 1.1.1
		[PEAR] XML_Util       - installed: 1.3.0
		[PEAR] PEAR           - installed: 1.10.1

		Wrote PEAR system config file at: /etc/php5/etc/pear.conf

		You may want to add: /etc/php5/lib/php to your php.ini include_path

		/root/php-5.6.29/build/shtool install -c ext/phar/phar.phar /etc/php5/bin
		ln -s -f phar.phar /etc/php5/bin/phar

		Installing PDO headers:           /etc/php5/include/php/ext/pdo/