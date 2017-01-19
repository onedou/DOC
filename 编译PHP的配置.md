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
		--with-readline