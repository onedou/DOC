### 1.首先准备需要逆向打包的ipa文件

### 2.新建一个空工程并打包

#### 1.打开Xcode编译器，新建一个工程（不需要写任何代码），Bunndle Identifier 跟需要逆向打包的ipa文件的一样，查看Bunndle Identifier需要将ipa文件解压（ipa本身是个压缩包），解压之后会在同目录下生成Payload文件夹如图1

![image](https://user-images.githubusercontent.com/3422640/155256758-f1f3f537-bf56-409d-887c-01df7bbe6760.png)

图1

#### 2.在文件夹下面会有个跟项目名字一样的文件再次解压，结果如图2
<img src="https://user-images.githubusercontent.com/3422640/155257042-6237cbfb-8df6-41a6-af5b-24e434f6c202.png" width="350">

图2

#### 3.在图2中可以选择替换icon给重新签名的ipa改图标，打开plist文件如图3 也可以选择修改其中的选项，拿到Bunndle Identifier 
<img src="https://user-images.githubusercontent.com/3422640/155257070-6a170101-5404-4d92-a524-ad0bcaae0c3d.png" width="650">

图三

#### 4.生成证书和配置文件（具体百度），打包空工程App得到ipa文件，然后走1.2步流程拿到  .mobileprovision文件（既是需要重新签名的配置文件）如图4，存放在一个地方（一会儿要用）
<img src="https://user-images.githubusercontent.com/3422640/155257103-0870e75c-0942-47f9-968d-2f275c5e4178.png" width="350">

图4

### 3.安装Homebrew

在终端先后执行下面2命令行安装，等待进度完毕

```
xcode-select --install
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

### 4.安装ruby
在终端执行下面命令安装ruby，等待进度完毕（输完密码可能在较短时间无反应）

```brew install ruby```

### 5.安装sigh脚本
执行下面安装命令

```
sudo gem install sigh
```

在安装过程中可能会报镜像源错误：

![image](https://user-images.githubusercontent.com/3422640/155257341-f836f8b0-6846-4573-a265-18fb023c5e08.png)

图5

终端输入

```
gem sources --add https://gems.ruby-china.com/  --remove https://gems.ruby-china.org/ 
```

回车替换镜像源文件。

查看镜像：```gem sources -l```

如果是：
![image](https://user-images.githubusercontent.com/3422640/155257420-d0e195d5-8505-42a5-86fe-3edec684be54.png)

图6

表明安装成功。
然后：```sudo gem install sigh``` 回车并输入电脑密码。

![image](https://user-images.githubusercontent.com/3422640/155257459-376dfe34-6a9a-4415-a989-29ee128e6cd3.png)

图7

表示安装成功。

然后：```sign resign``` 会出现下图的情况 替换成 ```fastlane sign resign``` 命令回车
![image](https://user-images.githubusercontent.com/3422640/155257510-78439f2c-a256-4355-8d48-122f4c2f7b84.png)

图8

![image](https://user-images.githubusercontent.com/3422640/155257529-aac23d2f-36f7-4825-a183-c9f79b7b0ea7.png)

图9
