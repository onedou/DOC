原文链接：http://blog.csdn.net/lilin_esri/article/details/70036737

## Tomcat配置ssl的443端口

要想使用tomcat的https必需要生成一个证书。  
这个证书的生成就需要依靠JDK, JDK中有一个**keyTool**可以用来生成我们需要的证书。

### 证书的生成方法：

#### 1. 首先，进入到cmd窗口，运行命令：

进入到 %JAVA_HOME%/bin 目录，这里JDK装在了D盘。执行命令：
（Mac 使用 ls -l `whick java`命令查看java目录）

![image](https://user-images.githubusercontent.com/3422640/30814232-29dfce3c-a242-11e7-84ab-cdb8b4819b07.png)

**参数说明：**

    -alias：F:\tomcat.keystore：证书文件保存的位置，名称是tomcat.keystore
    -validity：证书的有效期36500是100年，默认是90天

#### 2. 输入证书参数：

回车会出现证书的必要参数：

![image](https://user-images.githubusercontent.com/3422640/30837469-ca407a88-a22a-11e7-9bf4-ea530522b0c2.png)

之后系统会跟你确认之前输入的信息是否正确，如果符合要去就输入y,不符合就输入n, 重新填写上面的信息。

确认信息正确后，输入tomcat的秘钥口令，和秘钥库口令相同，直接回车。    
查看F盘是否生成了tomcat.keystore的文件了。    
生成证书之后，就可以去修改tomcat的配置文件了。  

### 配置Tomcat：

进入Tomcat的安装目录，找到conf目录下的server.xml，打开编辑：

![image](https://user-images.githubusercontent.com/3422640/30837542-36f3b0be-a22b-11e7-94e3-1edb68afe173.png)

keystoreFile是你的证书的存放位置。
keystorePass是之前设置的秘钥库口令，由于我设置的tomcat的秘钥口令和秘钥库口令相同，这里就是相同的。编辑完成，保存。
接着，修改tomcat的bin目录下的service.bat文件，

添加JAVA_HOEM,CATALINA_HOME,PR_DISPLAYNAME:这三个分别是jdk的目录，tomcat的目录和tomcat显示的服务名称。
为了使用tomcat更方便，将其注册成服务：
注册方法：进入命令行，cd到tomcat的bin目录下。service.bat文件就是我们注册系统服务所要使用的文件。执行命令service.bat install 服务名，就可以看到提示信息了。
提示服务安装成功后，就可以去看看系统服务中是否已经有我们刚刚注册的服务了。
如果以后不想要这个服务了，就执行命令service.bat uninstall 服务名 移除就可以了。
