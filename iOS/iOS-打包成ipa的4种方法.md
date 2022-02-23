## 第一种
《iOS-最全的App上架教程》  
https://www.jianshu.com/p/cea762105f7c  
上面文章已经提到了第一种 也是 最为实用的一种 打包上架api的方式，这里就不多少了。  

### 打包ipa的前提

1、证书的申请和设置和上面文章的一样，从第一步到第四步都是一样的（http://www.jianshu.com/p/cea762105f7c）  
2、还有第六步的 1-3都是一样的 从第四步开始变化  

## 第二种打包api的方法：

通过生成文件Payload文件夹，生成ipa包。  

1、在打包ipa的前提条件都弄好之后，Command+B 编译  
![image](https://user-images.githubusercontent.com/3422640/35200851-04945448-ff50-11e7-84a3-11769b28f181.png)


2、然后按图 操作  
![image](https://user-images.githubusercontent.com/3422640/35200857-0ef634d8-ff50-11e7-9165-ed0214d5682e.png)
![image](https://user-images.githubusercontent.com/3422640/35200859-1c1a5e3c-ff50-11e7-8ab3-4465b6035a85.png)


3、在桌面上新建一个文件夹名字叫“Payload”，注意一个字母也不能少。并将上面的APP直接拷贝到这个文件夹下面，压缩这个文件夹，并将文件夹的后缀名，改正 “.ipa”。如下图:  

![image](https://user-images.githubusercontent.com/3422640/35200863-27ccfe56-ff50-11e7-8de6-c7e573194a1c.png)
![image](https://user-images.githubusercontent.com/3422640/35200884-6de7b9da-ff50-11e7-87dd-811fa01a0340.png)


## 第三种打包api的方法：通过iTunes，打包

1、直接把刚刚的那个 .app，拖到你的iTunes里面。如下图:

![image](https://user-images.githubusercontent.com/3422640/35200922-e442a766-ff50-11e7-8109-1eb0eb299478.png)

2、在Finder里面显示：

![image](https://user-images.githubusercontent.com/3422640/35200927-fccbdf46-ff50-11e7-9a53-f69cd8ea82df.png)

3、生成ipa

![image](https://user-images.githubusercontent.com/3422640/35200928-03c462b4-ff51-11e7-9f28-61830e4752ae.png)


## 第四种打包api的方法：Xcode插件管理工具Alcatraz法

如果没有安装Alcatraz工具的可以查看Alcatraz工具安装教程

1、在插件Xcode插件管理工具Alcatraz之上，插件名字叫：AMAppExportToIPA 。直接ipa 就出来了 然后安装

![image](https://user-images.githubusercontent.com/3422640/35200940-2994f788-ff51-11e7-8344-113f731f55db.png)
![image](https://user-images.githubusercontent.com/3422640/35200945-31667bee-ff51-11e7-8cc1-94838d45c981.png)

2、找到要打包的app 然后点击Export IPA

![image](https://user-images.githubusercontent.com/3422640/35200948-43df67cc-ff51-11e7-86b2-058df6b231ee.png)

3、然后在桌面找到AM_Builds 文件夹 打开就是 生成好的ipa文件

![image](https://user-images.githubusercontent.com/3422640/35200970-85c55278-ff51-11e7-8aca-00184e704dda.png)
![image](https://user-images.githubusercontent.com/3422640/35200972-89bb3348-ff51-11e7-83ef-e97455094343.png)


如果你打包的是 测试的ipa文件 那个如何 将其安装到手机里面呢？
对于以上生成的所有的ipa包，都需要双击打开他们，在你的iTunes里面，安装你的这个应用包。如下图：

![image](https://user-images.githubusercontent.com/3422640/35200977-93daf8ea-ff51-11e7-9c1b-b4f0d1d7c08d.png)

