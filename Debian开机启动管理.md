##Debian开机启动管理

linux下，services的启动、停止等通常是通过/etc/init.d的目录下的脚本来控制的。  
在启动或改变运行级别是在/etc/rcX.d中来搜索脚本。其中X是运行级别。  

比如Apache2，安装完成后，默认或启动。  
比如我安装了vagrant的LMPA的box。  
需要禁止掉自启动，就需要禁止掉这个服务，然后在需要的时候使用

	/usr/sbin/apachectl start
	/etc/init.d/apache2 start

在debian中使用 update-rc.d命令来实现

	update-rc.d [-n] [-f] <basename> remove
	update-rc.d [-n] <basename> defaults [NN | SS KK]
	update-rc.d [-n] <basename> start|stop NN runlvl [runlvl] [...] .
	update-rc.d [-n] <basename> disable|enable [S|2|3|4|5]
	-n: not really
	-f: force


>删除服务

    update-rc.d -f apache2 remove # -f 为强制删除

>添加服务

	update-rc.d apache2 defaults

并且可以指定该服务的启动顺序：

	update-rc.d apache2 defaults 90

还可以更详细的控制start与kill顺序：

	update-rc.d apache2 defaults 20 80

其中前面的20是start时的运行顺序级别，80为kill时的级别。也可以写成：

	update-rc.d apache2 start 20 2 3 4 5 . stop 80 0 1 6 .

其中0～6为运行级别。 update-rc.d命令不仅适用Linux服务，编写的脚本同样可以用这个命令设为开机自动运行

在debian6中使用update-rc.d会报错，如下：

	update-rc.d: using dependency based boot sequencing

可以使用 insserv 命令来代替 update-rc.d

	insserv [<options>] [init_script|init_directory]
	Available options:
	-h, --help       This help.
	-r, --remove     Remove the listed scripts from all runlevels.
	-f, --force      Ignore if a required service is missed.
	-v, --verbose    Provide information on what is being done.
	-p <path>, --path <path>  Path to replace /etc/init.d.
	-o <path>, --override <path> Path to replace /etc/insserv/overrides.
	-c <config>, --config <config>  Path to config file.
	-n, --dryrun     Do not change the system, only talk about it.
	-d, --default    Use default runlevels a defined in the scripts

比如

	insserv -r apache2

