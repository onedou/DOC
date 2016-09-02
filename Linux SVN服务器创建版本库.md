# Linux SVN服务器创建版本库 #

## 1、安装svn服务 ##

	yum install subversion

## 2、新建一个目录用于存储SVN所有文件 ##

	mkdir /home/svn

## 3、在上面创建的文件夹中为项目 project_1 创建一个版本仓库 ##

	svnadmin create /home/svn/project_1

执行完这一步，/home/svn中将存在project_1文件夹，这个项目的配置都在 /home/svn/project_1/conf 中

## 4、为项目配置权限 ##

>（1）svnserve.conf 是主配置文件

	vi/home/svn/project_1/conf/svnserve.conf
	anon-access=read         #匿名可读
	auth-access=write        #验证用户可读写
	password-db=passwd       #指向验证用户名密码的数据文件 passwd ，请看下文配置
	auth-db=authz            #指向验证用户的权限配置文件 authz ，请看下文配置

注意：每一行前方不能有空格，否则会出现 Option expected错误！

>（2）passwd用户名密码配置文件 

	vi/home/svn/project_1/conf/passwd
	[users]
	manager1=123456      #每一行都要是“用户名=密码”的格式
	manager2=123123 
	manager3=888888

>（3）authz用户权限配置文件 

	vi/home/svn/project_1/conf/authz
	[groups]
	managers=manager1,manager2        #定义群组 managers 包含 manager1 和 manager2 两个用户
	[/]
	@managers=rw                      #定义群组 managers 有读写权限
	manager3=r                        #定义 manager3 有读权限
	*=                                #以上没有定义的用户都没有任何权限

## 5、启动服务器 ##
	svnserve -d -r /home/svn

开启多个版本库的时候要启动不同的端口 svnserve -d --listen-port 3688 -r /static/