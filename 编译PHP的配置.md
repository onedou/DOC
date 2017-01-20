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