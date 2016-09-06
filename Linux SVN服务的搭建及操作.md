#Linux SVN 服务的搭建及操作

##一. 使用yum安装SVN服务 

###1.安装svn命令如下

	[root@shxt~ ]# yum install -y subversion //验证安装版本 
	[root@shxt ~]# svnserve --version //创建SVN 版本库 
	[root@shxt ~]# mkdir /var/www/svn
	[root@shxt ~]# svnadmin create /var/www/svn/testproject -- testproject <--为版本库名称 //为svn创建用户 
	[root@shxt ~]# htpasswd -c /var/www/passwd *** //这个根据情况不同，写法不同,-c是创建用户（删除原有用户）,-d是在原有基础上添加用户

###2.配置svn 

创建版本库后，在这个目录下会生成3个配置文件： 

	[root@shxt conf]# pwd
	/var/www/svn/testproject/conf  //显示当前目录的绝对路径

	[root@shxt conf]# ls 
	authz  passwd  svnserve.conf 

svnserve.conf 文件，该文件配置项分为以下5项： 

	anon-access ： 控制非鉴权用户访问版本库的权限。 
	auth-access ： 控制鉴权用户访问版本库的权限。 
	password-db ： 指定用户名口令文件名。 
	authz-db    ： 指定权限配置文件名，通过该文件可以实现以路径为基础的访问控制。 
	realm       ： 指定版本库的认证域，即在登录时提示的认证域名称。若两个版本库的认证域相同，建议使用相同的用户名口令数据文件 

Passwd 文件，在配置此文件前需要在svnserve.conf文件中启用此文件，Passwd配置如下： 

	[root@shxt conf]# vim passwd 
	[users]
	# harry = harryssecret 
	# sally = sallyssecret 
	admin = admin 
	big= big 

authz 文件，下面我们来配置我们的authz文件：  

	[root@shxt conf]# vim authz

	[groups] 
	admin = admin 
	zhangsan=zhangsan

	[project:/] 
	@admin = rw 
	@zhangsan = rw

	[root@shxt conf]# 

以下是在网上找到一个很好的配置例子： 

	[groups] 
	admin = john, kate 
	devteam1 = john, rachel, sally 
	devteam2 = kate, peter, mark 
	docs = bob, jane, mike 
	training = zak 

这里把不同用户放到不同的组里面，下面在设置目录访问权限的时候，用目录来操作就可以了。 

	# 为所有库指定默认访问规则 
	# 所有人可以读，管理员可以写，危险分子没有任何权限 
	[/] --对应我测试里的：/u02/svn 目录
	* = r 
	@admin = rw 
	dangerman =
	
	# 允许开发人员可以完全访问他们的项目版本库 
	[proj1:/]
	@devteam1 = rw
	[proj2:/]
	@devteam2 = rw
	[bigproj:/]
	@devteam1 = rw
	@devteam2 = rw
	trevor = rw
	
	# 文档编写人员对所有的docs目录有写权限 
	[/trunk/doc] 
	@docs = rw 
	
	# 培训人员可以完全访问培训版本库 
	[TrainingRepos:/] 
	@training = rw 

####4.启动和停止SVN服务 

#####（1）启动SVN服务: 

	[root@shxt conf]# svnserve -d -r /var/www/svn 
	-d 表示后台运行
	-r 指定根目录是 /var/www/svn 

#####（2）查看svn服务 

	[root@shxt conf]# ps -ef | grep svn

#####（3）停止SVN服务: 

	[root@shxt conf]# ps -aux |grep svn 
	[root@shxt conf]# kill -9 进程杀掉 

>多数时候会把svn服务放到apache的服务中,重启apache

	/usr/local/apache/bin/apachectl restart 
	或者 
	service httpd restart

如果遇到下列问题

	Can't open file '/var/www/svn/repo_name/db/txn-current-lock': Permission denied 

需要分配读写权限

$ cd /var/www/svn 
    $ chown -R apache.apache project（项目名） 
  或者 
     $ chmod –R o+rw  /var/www/svn/

##二. svnadmin操作命令
因为svnadmin直接访问版本库（因此只可以在存放版本库的机器上使用），它通过路径访问版本库，而不是URL。

###1.创建SVN版本库

1.新建一个目录用于存储SVN所有文件

	# mkdir /home/svn

2、在上面创建的文件夹中为项目 project_1 创建一个版本仓库

	# svnadmin create /opt/svn/project_1

>执行完这一步，/opt/svn中将存在project_1文件夹，这个项目的配置都在 /home/svn/project_1/conf 中

###2.svnadmin命令

####svnadmin 参数
	--bdb-log-keep （Berkeley DB特定）关闭数据库日志自动日志删除功能。
	--bdb-txn-nosync （Berkeley DB特定）当提交数据库事务时关闭fsync。
	--bypass-hooks 绕过版本库钩子系统。
	--clean-logs 删除不使用的Berkeley DB日志。
	--force-uuid 缺省情况下，当版本库加载已经包含修订版本的数据时，svnadmin会忽略流中的UUID，这个选项会导致版本库的UUID设置为流的UUID。
	--ignore-uuid 缺省情况下，当加载空版本库时，svnadmin会使用来自流中的UUID，这个选项会导致忽略UUID。
	--incremental 导出一个修订版本针对前一个修订版本的区别，而不是通常的完全结果。
	--parent-dir DIR 当加载一个转储文件时，根路径为DIR而不是。
	--revision (-r) ARG 指定一个操作的修订版本。
	--quiet	不显示通常的过程—只显示错误。
	--use-post-commit-hook 当导入使用一个转储文件时，在每次新的修订版本产生时运行版本库post-commit钩子。
	--use-pre-commit-hook 当加载一个转储文件时，每次新加修订版本之前运行版本库的pre-commit钩子。如果钩子失败，终止提交并中断加载进程。

####svnadmin 子命令

#####Create命令

**名称：**svnadmin create 创建一个新的空的版本库。  
**概要：**svnadmin create REPOS_PATH  
**描述：**在提供的路径上创建一个新的空的版本库，如果提供的目录不存在，它会为你创建。  

>对于Subversion 1.2，svnadmin缺省使用fsfs文件系统后端创建版本库。

**选项：**

	--bdb-txn-nosync
	--bdb-log-keep
	--config-dir DIR
	--fs-type TYPE

**例子：**

创建一个版本库就是这样简单

	$ svnadmin create /usr/local/svn/repos

在Subversion 1.0，一定会创建一个Berkeley DB版本库，在Subversion 1.1，Berkeley DB版本库是缺省类型，但是一个FSFS版本库也是可以创建，使用--fs-type选项：

	$ svnadmin create /usr/local/svn/repos --fs-type fsfs

>记住svnadmin只工作在本地路径，而不是URL。

#####Help命令

**名称：**svnadmin help  
**概要：**svnadmin help [SUBCOMMAND...]  
**描述：**当你困于一个没有网络连接和本书的沙漠岛屿时，这个子命令非常有用。  
**别名：**h  

#####Hotcopy命令

**名称：**svnadmin hotcopy — 制作一个版本库的热备份。  
**概要：**svnadmin hotcopy REPOS_PATH NEW_REPOS_PATH  
**描述：**这个子命令会制作一个版本库的完全“热”拷贝，包括所有的钩子，配置文件，当然还有数据库文件。如果你传递--clean-logs选项，svnadmin会执行热拷贝操作，然后删除不用的Berkeley DB日志文件。你可以在任何时候运行这个命令得到一个版本库的安全拷贝，不管其它进程是否使用这个版本库。  
**选项：**--clean-logs  

#####List命令

**名称：**svnadmin list-dblogs — 询问Berkeley DB在给定的Subversion版本库有哪些日志文件存在（只有在版本库使用bdb作为后端时使用）。  
**概要：**svnadmin list-dblogs REPOS_PATH  
**描述：**Berkeley DB创建了记录所有版本库修改的日志，允许我们在面对大灾难时恢复。除非你开启了DB_LOG_AUTOREMOVE，否则日志文件会累积，尽管大多数是不再使用可以从磁盘删除得到空间。  

#####List-unused-dblogs命令

**名称：**svnadmin list-unused-dblogs — 询问Berkeley DB哪些日志文件可以安全的删除（只有在版本库使用bdb作为后端时使用）。  
**概要：**svnadmin list-unused-dblogs REPOS_PATH  
**描述：**Berkeley DB创建了记录所有版本库修改的日志，允许我们在面对大灾难时恢复。除非你开启了DB_LOG_AUTOREMOVE，否则日志文件会累积，尽管大多数是不再使用，可以从磁盘删除得到空间。  
**例子：**删除所有不用的日志文件：  

	$ svnadmin list-unused-dblogs /path/to/repos
	/path/to/repos/log.0000000031
	/path/to/repos/log.0000000032
	/path/to/repos/log.0000000033

	$ svnadmin list-unused-dblogs /path/to/repos | xargs rm
	## disk space reclaimed!


**名称：**svnadmin load — 从标准输出读取“转储文件”格式流。  
**概要：**svnadmin load REPOS_PATH  
**描述：**从标准输出读取“转储文件”格式流，提交新的修订版本到版本库文件系统，发送进展反馈到标准输出。  
**选项：**

	--quiet (-q)
	--ignore-uuid
	--force-uuid
	--use-pre-commit-hook
	--use-post-commit-hook
	--parent-dir

**例子：**这里显示了加载一个备份文件到版本库,当然，使用

	svnadmin dump：
	$ svnadmin load /usr/local/svn/restored < repos-backup
	<<< Started new txn, based on original revision 1
	     * adding path : test ... done.
	     * adding path : test/a ... done.
	…

或者你希望加载到一个子目录：

	$ svnadmin load --parent-dir new/subdir/for/project /usr/local/svn/restored < repos-backup
	<<< Started new txn, based on original revision 1
	     * adding path : test ... done.
	     * adding path : test/a ... done.
	…

#####lslocks命令

**名称：**svnadmin lslocks — 打印所有锁定的描述。  
**概要：**svnadmin lslocks REPOS_PATH  
**描述：**打印版本库所有锁定的描述。  
**选项：**无  
**例子：**显示了版本库/svn/repos中一个锁定的文件：  

	$ svnadmin lslocks /svn/repos
	Path: /tree.jpg
	UUID Token: opaquelocktoken:ab00ddf0-6afb-0310-9cd0-dda813329753
	Owner: harry
	Created: 2005-07-08 17:27:36 -0500 (Fri, 08 Jul 2005)
	Expires: 
	Comment (1 line):
	Rework the uppermost branches on the bald cypress in the foreground.

#####lstxns命令

**名称：**svnadmin lstxns — 打印所有未提交的事物名称。  
**概要：**svnadmin lstxns REPOS_PATH  
**描述：**打印所有未提交的事物名称。关于未提交事物是怎样创建和如何使用的信息见“版本库清理”一节。  
**例子：**列出版本库所有突出的事物。  

	$ svnadmin lstxns /usr/local/svn/repos/ 
	1w
	1x

#####recover命令

**名称：**svnadmin recover — 将版本库数据库恢复到稳定状态（只有在版本库使用bdb作为后端时使用），此外，如果repos/conf/passwd不存在，它会创建一个默认的密码文件。  
**概要：**svnadmin recover REPOS_PATH  
**描述：**在你得到的错误说明你需要恢复版本库时运行这个命令。  
**选项：**--wait  
**例子：**恢复挂起的版本库：  

	$ svnadmin recover /usr/local/svn/repos/ 
	Repository lock acquired.
	Please wait; recovering the repository may take some time...
	
	Recovery completed.
	The latest repos revision is 34.
	恢复数据库需要一个版本库的独占锁（这是一个“
	
	数据库锁”；见“锁定”的三种含义），如果另一个进程访问版本库，svnadmin recover会出错：
	$ svnadmin recover /usr/local/svn/repos
	svn: Failed to get exclusive repository access; perhaps another process
	such as httpd, svnserve or svn has it open?
	
	$
	--wait选项可以导致
	
	svnadmin recover一直等待其它进程断开连接：
	$ svnadmin recover /usr/local/svn/repos --wait
	Waiting on repository lock; perhaps another process has it open?
	
	### time goes by...
	
	Repository lock acquired.
	Please wait; recovering the repository may take some time...
	
	Recovery completed.
	The latest repos revision is 34.

#####rmlocks命令

**名称：**svnadmin rmlocks — 无条件的删除版本库的一个或多个锁定。
**概要：**svnadmin rmlocks REPOS_PATH LOCKED_PATH...
**描述：**从LOCKED_PATH删除没个锁定。
**选项：**无
**例子：**这删除了版本库/svn/repos里tree.jpg和house.jpg文件上的锁定：

	$ svnadmin rmlocks /svn/repos tree.jpg house.jpg
	Removed lock on '/tree.jpg.
	Removed lock on '/house.jpg.

#####rmtxns命令

**名称：**svnadmin rmtxns — 从版本库删除事物。  
**概要：**svnadmin rmtxns REPOS_PATH TXN_NAME...  
**描述：**删除版本库突出的事物。   
**选项：**--quiet (-q)  
**例子：**删除命名的事物：  

	$ svnadmin rmtxns /usr/local/svn/repos/ 1w 1x

很幸运，lstxns的输出作为rmtxns输入工作良好：

	$ svnadmin rmtxns /usr/local/svn/repos/  `svnadmin lstxns /usr/local/svn/repos/`
	从版本库删除所有未提交的事务。

#####setlog命令

**名称：**svnadmin setlog — 设置某个修订版本的日志信息。  
**概要：**svnadmin setlog REPOS_PATH -r REVISION FILE  
**描述：**设置修订版本REVISION的日志信息为FILE的内容。 这与使用svn propset --revprop设置某一修订版本的svn:log属性效果一样，除了你也可以使用--bypass-hooks选项绕过的所有pre-或post-commit的钩子脚本，这在pre-revprop-change钩子脚本中禁止修改修订版本属性时非常有用。
**警告：**修订版本属性不在版本控制之下的，所以这个命令会永久覆盖前一个日志信息。
**选项:**

	--revision (-r) ARG
	--bypass-hooks

**例子：**设置修订版本19的日志信息为文件msg的内容：

	$ svnadmin setlog /usr/local/svn/repos/ -r 19 msg

#####verify命令

**名称：**svnadmin verify — 验证版本库保存的数据。  
**概要：**svnadmin verify REPOS_PATH  
**描述：**如果希望验证版本库的完整性可以运行这个命令，原理是通过在内部转储遍历所有的修订版本并且丢掉输出。  
**例子：**检验挂起的版本库：  

	$ svnadmin verify /usr/local/svn/repos/ 
	* Verified revision 1729.

##三. Windows客户端连接SVN服务器 

###2.1 安装TortoiseSVN 客户端 

下载地址：[http://tortoisesvn.net/downloads.html](http://tortoisesvn.net/downloads.html) 

###2.2 找到自己项目的目录，右击，进行SVN 操作

（1）新建测试目录svn，进入后右键，点checkout。  
（2）天线SVN服务器的IP地址和版本库名称。  
（3）新建一个测试文件svn.txt. 把这个文件上传到SVN服务器(add)。   

##四.Linux下svn使用命令总结： 

###1、将文件checkout到本地目录 

	svn checkout path（path是服务器上的目录） 
	例如：svn checkout svn://192.168.1.1/pro/domain 
	简写：svn co
 
###2、往版本库中添加新的文件 

	svn add file 
	例如：svn add test.php(添加test.php) 
	svn add *.php(添加当前目录下所有的php文件) 

###3、将改动的文件提交到版本库 

	svn commit -m “LogMessage“ [-N] [--no-unlock] PATH(如果选择了保持锁，就使用–no-unlock开关) 

	例如：svn commit -m “add test file for my test“ test.php 
	简写：svn ci 

###4、加锁/解锁 

	svn lock -m “LockMessage“ [--force] PATH 

	例如：svn lock -m “lock test file“ test.php 
	svn unlock PATH 

###5、更新到某个版本 

	svn update -r m path
 
	例如： 
	svn update 如果后面没有目录，默认将当前目录以及子目录下的所有文件都更新到最新版本。 
	svn update -r 200 test.php (将版本库中的文件test.php还原到版本200) 
	svn update test.php 
	(更新，于版本库同步。如果在提交的时候提示过期的话，是因为冲突，
	需要先update，修改文件，然后清除svn resolved，最后再提交commit) 

	简写：svn up 

###6、查看文件或者目录状态

	svn status path（目录下的文件和子目录的状态，正常状态不显示）
	注：不在svn的控制中；M：内容被修改；C：发生冲突；A：预定加入到版本库；K：被锁定】 
	
	svn status -v path(显示文件和子目录状态) 
	第一列保持相同，第二列显示工作版本号，第三和第四列显示最后一次修改的版本号和修改人。 
	注：svn status、svn diff和 svn revert这三条命令在没有网络的情况下也可以执行的，
	   原因是svn在本地的.svn中保留了本地版本的原始拷贝。 

	简写：svn st 

###7、删除文件 

	svn delete path -m “delete test fle“ 

	例如：svn delete svn://192.168.1.1/pro/domain/test.php -m “delete test file” 
	或者直接svn delete test.php 然后再svn ci -m ‘delete test file‘，推荐使用这种 
	简写：svn (del, remove, rm)
 
###8、查看日志 

	svn log path 
	例如：svn log test.php 显示这个文件的所有修改记录，及其版本号的变化 

###9、查看文件详细信息 

	svn info path 
	例如：svn info test.php 

###10、比较差异 

	svn diff path(将修改的文件与基础版本比较) 
	例如：svn diff test.php 

	svn diff -r m:n path(对版本m和版本n比较差异) 
	例如：svn diff -r 200:201 test.php 
	简写：svn di 

###11、将两个版本之间的差异合并到当前文件 

	svn merge -r m:n path 
	例如：svn merge -r 200:205 test.php（将版本200与205之间的差异合并到当前文件，但是一般都会产生冲突，需要处理一下） 

###12、SVN 帮助 

	svn help 
	svn help ci 

-----

**以上是常用命令，下面写几个不经常用的**

-----

###13、版本库下的文件和目录列表 

	svn list path 

	显示path目录下的所有属于版本库的文件和目录 
	简写：svn ls 

###14、创建纳入版本控制下的新目录 

	svn mkdir 创建纳入版本控制下的新目录。 

	用法: 
	1、mkdir PATH… 
	2、mkdir URL… 
	创建版本控制的目录。 

	1、每一个以工作副本 PATH 指定的目录，都会创建在本地端，并且加入新增调度，以待下一次的提交。 
	2、每个以URL指定的目录，都会透过立即提交于仓库中创建。 
	在这两个情况下，所有的中间目录都必须事先存在。 

###15、恢复本地修改 

	svn revert 恢复原始未改变的工作副本文件 (恢复大部份的本地修改)。

	用法: revert PATH… 
	注意: 本子命令不会存取网络，并且会解除冲突的状况。但是它不会恢复被删除的目录

###16、代码库URL变更 

	svn switch (sw) 更新工作副本至不同的URL。 

	用法:
	1、switch URL [PATH] 
	更新你的工作副本，映射到一个新的URL，其行为跟“svn update”很像，
	也会将服务器上文件与本地文件合并。
	这是将工作副本对应到同一仓库中某个分支或者标记的方法。 

	2、switch –relocate FROM TO [PATH...] 
	改写工作副本的URL元数据，以反映单纯的URL上的改变。
	当仓库的根URL变动(比如方案名或是主机名称变动)，
	但是工作副本仍旧对映到同一仓库的同一目录时使用这个命令更新工作副本与仓库的对应关系。 

###17、解决冲突 

	svn resolved: 移除工作副本的目录或文件的“冲突”状态。

	用法: resolved PATH… 
	注意: 本子命令不会依语法来解决冲突或是移除冲突标记；它只是移除冲突的 
	相关文件，然后让 PATH 可以再次提交。 

###18、输出指定文件或URL的内容。 

	svn cat 目标[@版本]…如果指定了版本，将从指定的版本开始查找。 
	svn cat -r PREV filename > filename (PREV 是上一版本,也可以写具体版本号,这样输出结果是可以提交的) 