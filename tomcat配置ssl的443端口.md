http://blog.csdn.net/lilin_esri/article/details/70036737

最近遇到一个问题，需要把一个war包放到tomcat下运行，于是就自己安装了一个tomcat，将war包放到其下运行，访问war包的应用，发现报错，提示该应用必需使用https的443端口，于是，仔细查找了这方面的资料。
发现，要想使用tomcat的https必需要生成一个证书。这个证书的生成就需要依靠jdk,jdk中有一个keyTool可以用来生成我们需要的证书。
下面是证书的生成方法：
1>首先，进入到cmd窗口，运行命令：
进入到%JAVA_HOME%/bin目录，我自己的jdk装在了d盘。执行命令：

参数说明：
-alias:
F:\tomcat.keystore：证书文件保存的位置，名称是tomcat.keystore
-validity：证书的有效期36500是100年，默认是90天

2>运行完后
回车会出现证书的必要参数：

之后系统会跟你确认之前输入的信息是否正确，如果符合要去就输入y,不符合就输入n,重新填写上面的信息。
确认信息正确后，输入tomcat的秘钥口令，和秘钥库口令相同，直接回车
然后就可以去F盘查看是否生成了tomcat.keystore的文件了。
生成证书之后，就可以去修改tomcat的配置文件了。进入tomcat的安装目录，找到conf目录下的server.xml，打开编辑，

keystoreFile是你的证书的存放位置，放在哪都可以，只要你可以通过这个目录找到它。
keystorePass是之前设置的秘钥库口令，由于我设置的tomcat的秘钥口令和秘钥库口令相同，这里就是相同的。编辑完成，保存。
接着，修改tomcat的bin目录下的service.bat文件，

添加JAVA_HOEM,CATALINA_HOME,PR_DISPLAYNAME:这三个分别是jdk的目录，tomcat的目录和tomcat显示的服务名称。
为了使用tomcat更方便，将其注册成服务：
注册方法：进入命令行，cd到tomcat的bin目录下。service.bat文件就是我们注册系统服务所要使用的文件。执行命令service.bat install 服务名，就可以看到提示信息了。
提示服务安装成功后，就可以去看看系统服务中是否已经有我们刚刚注册的服务了。
如果以后不想要这个服务了，就执行命令service.bat uninstall 服务名 移除就可以了。
