#Linux 软连接的操作

##系统环境变量路径

	/usr/bin
	
	MAC:/usr/local/bin

##删除软连接

	rm -rf 软连接名称

##建立软连接

	ln -s a b

其中的 a 就是源文件，b是链接文件名,其作用是当进入b目录，实际上是链接进入了a目录

##查看所有软连接
	ls -la
