# 手动编译PHP7的MongoDB扩展

>下载支持php7的MongoDB扩展

    wget http://pecl.php.net/get/mongodb-1.1.1.tgz
    tar -zxvf mongodb-1.1.1.tgz
    cd mongodb-1.1.1.tgz
    /usr/local/php7/bin/phpize
    ./configure --with-php-config=/usr/local/php7/bin/php-config
    make && make install

如果出现Can't install [sasl.h not found!]的错误，执行命令apt-get install libsasl2-dev。

添加mongodb.so到php.ini里

>查看当前扩展

    /usr/local/php7/bin/php -m
    [PHP Modules]
    bcmath
    Core
    ctype
    curl
    date
    dom
    filter
    ftp
    gd
    gettext
    hash
    iconv
    json
    libxml
    mbstring
    mcrypt
    mongodb
    mysqlnd
    openssl
    pcntl
    pcre
    PDO
    pdo_sqlite
    Phar
    posix
    Reflection
    session
    shmop
    SimpleXML
    soap
    sockets
    SPL
    sqlite3
    standard
    sysvsem
    tokenizer
    xml
    xmlreader
    xmlrpc
    xmlwriter
    Zend OPcache
    zip
    zlib

    [Zend Modules]
    Zend OPcache

可以看到已经有mongodb扩展了。
