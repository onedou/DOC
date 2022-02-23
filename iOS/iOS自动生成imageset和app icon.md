美术UI在公司是宝贵的资源，集各种项目宠爱于一身。为了努力完成好老板的进度需求，不给UI添麻烦。程序员开始忙活了。
在iOS里面，我们使用image assert来管理素材和app icon。为什么呢？因为方便，按照image assert要求的尺寸拖进去就好了。

![image](https://user-images.githubusercontent.com/3422640/155271586-44d348d9-2fa4-4a49-bb28-0cfa6a08dac4.png)

ImageAssert适配各种尺寸

### 1. 缩放图片到指定尺寸

用过PS么？用过美图秀秀么？好吧，没有我也不勉强你，估计你也不会美颜、磨皮、消下巴、变大眼睛、美白吧！用的很熟练，你也不用看这篇东东了。来吧，在Mac下给你一个神器sips，一个命令行工具，哈哈！sips这玩意是命令行处理图片大小的，可以方便的用来修改图片的各种尺寸。

![image](https://user-images.githubusercontent.com/3422640/155271935-2b30c222-ff36-4607-82d3-7042520e8277.png)

高分辨率的button图片素材

如果button.png像素为256x256，压成@2x和@1x的话，按照2/3、1/3来压缩得到171x171和85x85的素材。那么sips命令就是这样使用（在shell命令行里）:

```
cp button.png button@3x.png
sips -z 171 171 button.png --out button@2x.png
sips -z 85 85 button.png --out button.png
```

这样输出，在当前文件夹下，你可以得到获得 button.png\button@2x.png \buttong@3x.png三个素材图片了，把它们拖到image assert里面来管理吧。在iOS界面Storyboard中可以直接使用button图片，配合AutoLayout自动适配不同尺寸设备。

![image](https://user-images.githubusercontent.com/3422640/155272497-acb4ba05-2b7d-45f7-8a09-efd9ac8aafb8.png)

@2x和@1x的button-assert

sips的详细用法请打开Mac的命令行终端：

```
//执行sips －help查看详细用法
sips –help
```

### 2. 获取图片尺寸，等比例缩放图片

上面我们是预先知道了图片的宽和高，然后自己进行了宽和高乘以2/3、1/3的处理，但是如果图片素材很多，你总不可能一个一个的搞几天啊，这不科学。我们是光荣伟大的Coder乜！嗯嗯，sips会给我们答案的，使用下面两行命令分别获取图片素材的宽和高：

```
sips -g pixelHeight button.png | awk -F: '{print $2}' sips -g pixelWidth button.png | awk -F: '{print $2}'
```

请实际将这两行代码运行一下，看看是否得到预期结果。sips输出宽高，然后awk命令把值提取出来。具体的参数含义请查一下手册。 接下来，我们需要地方来存储这两个变量，并计算出它们乘以2/3、1/3的结果。因此，我们来写个Shell脚本程序吧！ Shell脚本，你就理解能把命令行程序打包组合执行的这么一个东东吧。 在Shell里面定义一个函数，命名为ScalePic

```
ScalePic () {
# 1. 获取图片的高和宽
imageHeight=`sips -g pixelHeight $1 | awk -F: '{print $2}'`imageWidth=`sips -g pixelWidth $1 | awk -F: '{print $2}'`height=`echo $imageHeight`width=`echo $imageWidth`
# 2. 获取压缩2/3和1/3后的尺寸
height2x=$(($height*2/3))width2x=$(($width*2/3))height1x=$(($height/3))width1x=$(($width/3))
# 3. 存放输入文件名，并生成@2x和@3x后缀文件名
imageFile=$1fileName2x=${imageFile/\.png/@2x\.png}fileName3x=${imageFile/\.png/@3x\.png}
# 4. 拷贝并进行压缩
cp $1 $fileName3xsips -z $height2x $width2x $1 --out $fileName2xsips -z $height1x $width1x $1
}
```

接下来我们如何在Shell脚本里面调用这个函数呢？我们首先来确定一下我们可爱尊贵的UI MM&GG给我们素材。都统统放在了一个文件夹里，有11.png、112.png 等感人肺腑的命名文件命名，好了。我们默默的把文件名改好。 打开命令行，进入跟这个文件夹一级的文件夹，将Shell脚本放入。Shell脚本是这个样子滴：

```
!/bin/sh
# 0. 进入素材文件夹
cd $1
# 1. 遍历当前文件夹下的所有文件，即所有图片素材了。
for file in ./* do
# 2. 获取图片的文件名，并生成 “文件名.imageset”文件夹，方便下一步处理
imageFile=$(basename $file) imageDir=${imageFile/.png/.imageset} mkdir $imageDir
# 3. 将图片拷贝入“文件名.imageset”文件夹，并进入该文件夹
cp $imageFile $imageDir/cd $imageDir
# 4. 执行ScalePic函数，将图片文件名作为参数。最后处理完后，退回上一级目录
ScalePic $imageFilecd ..

done
cd ..
```
大家可以试着运行一下Shell脚本，脚本名字imagesetGenerator.sh，素材图片的文件夹为imageiphone，命令行下执行脚本：

```
./imagesetGenerator.sh imageiphone
```

哇塞！一瞬间，所有的大小尺寸的图片都生成了，并在各自的文件夹下了！wonderful！

### 3. 真的要手动把所有图片拖入到ImageSet里面吗
手动生成了所有图片素材后，你以为工作就结束了吗？试试将所有的素材拖入到ImageSet里面吧，工作是痛苦而乏味的。我们能这样弱吗？答案明显是NO，我们是光荣伟大的Coder乜！ 首先，在Xcode里面右键，打开一个ImageSet文件夹。

![image](https://user-images.githubusercontent.com/3422640/155273296-c12954f3-6653-4097-9a11-9d16a09b52fa.png)

在Finder中打开ImageSet-1024x482

如下图所示，ImageSet文件夹都是以“.imageset”结尾的，里面包含三个图像素材和一个“Contents.json”文件。

![image](https://user-images.githubusercontent.com/3422640/155273336-44c7a50d-f2ab-417d-9c68-14e9f2df6161.png)

ImageSet结构

我们打开“Contents.json”文件，里面的结构如下：

```
{
	"images": [{
		"idiom": "universal",
		"scale": "1x",
		"filename": "btn_close.png"
	}, {
		"idiom": "universal",
		"scale": "2x",
		"filename": "btn_close@2x.png"
	}, {
		"idiom": "universal",
		"scale": "3x",
		"filename": "btn_close@3x.png"
	}],
	"info": {
		"version": 1,
		"author": "xcode"
	}
}
```

这是一个以JSON格式表述的素材管理格式。这个JSON文件的内容很容易看懂的，基本上就是1x，2x和3x对应的图像文件名。因此我们就要生成这样的一个Contents.json文件，并放入相应的有各种图像素材的imageset文件夹里。 好，回到我们现在的工作阶段。上一步2的脚本里面，我们已经把图像的素材放入了.iamgeset文件夹里了，我们现在就差一个Contents.json描述文件了。继续搞起！

```
contents () {
  imageFile=$1
  renameFile2x=${imageFile/.png/@2x.png}
  renameFile3x=${imageFile/.png/@3x.png}
  echo { >> Contents.json
  echo " \"images\"" : [>> Contents.json
  echo " "{>> Contents.jsonecho " \"idiom\"" : "\"universal\"",>> Contents.json
  echo " \"scale\"" : "\"1x\"",>> Contents.json
  echo " \"filename\"" : "\"$imageFile\"">> Contents.json
  echo " "},>> Contents.json
  echo " "{>> Contents.json
  echo " \"idiom\"" : "\"universal\"",>> Contents.json
  echo " \"scale\"" : "\"2x\"",>> Contents.json
  echo " \"filename\"" : "\"$renameFile2x\"">> Contents.json
  echo " "},>> Contents.jsonecho " "{>> Contents.json
  echo " \"idiom\"" : "\"universal\"",>> Contents.json
  echo " \"scale\"" : "\"3x\"",>> Contents.json
  echo " \"filename\"" : "\"$renameFile3x\"">> Contents.json
  echo " "}>> Contents.jsonecho " "],>> Contents.json
  echo " \"info\"" : {>> Contents.json
  echo " \"version\"" : 1,>> Contents.json
  echo " \"author\"" : "\"xcode\"">> Contents.json
  echo " "}>> Contents.jsonecho } >> Contents.json
}
```

contents函数，暴力的echo文本到Contents.json文件中去。 好了，最后的完成态的脚本应该是这个样子滴：

```
!/bin/sh
# 0. 进入需要处理的素材文件夹
cd $1
# 1. 遍历当前文件夹下的所有文件，即所有图片素材了。
for file in ./* do
# 2. 获取图片的文件名，并生成 “文件名.imageset”文件夹，方便下一步处理
imageFile=$(basename $file)
imageDir=${imageFile/.png/.imageset}
mkdir $imageDir
# 3. 将图片拷贝入“文件名.imageset”文件夹，并进入该文件夹
cp $imageFile $imageDir/
cd $imageDir
# 4. 执行ScalePic函数，将图片文件名作为参数。
# 执行Contents函数，生成描述文件Contents.json
# 最后处理完后，退回上一级目录
ScalePic $imageFileContents $imageFilecd ..

done
cd ..
```

执行后，得到所需要的文件素材，拖入XCode工程的Assets.xcassets文件夹中，就可以在项目中自动识别出来了。
完成的脚本在GitHub上，可以点击获取imagesetGenerator.sh。
调用过程如下(可能简书没法显示GIF，可以去我的博客站看看)：

![image](https://user-images.githubusercontent.com/3422640/155274829-644c6aa5-0805-4dac-85ac-b10bcd17d29e.png)

### 4. 彩蛋，AppIcon也能这么干！
ImageSet里面AppIcon里面需要匹配的尺寸更多，我们当然也可以轻松的解决。脚本在下面了，怎么用和理解当成作业留给大家了。

```
!/bin/sh
IconWithSize() {
  #-Z 等比例按照给定尺寸缩放最长边。
  sips -Z $1 icon.png --out icon_$1x$1.png
}

for size in 29 40 50 57 58 60 72 76 80 87 100 114 120 144 152 180 do
  IconWithSize $size
done

```

希望大家能够利用工具简化一切操作！我们是光荣伟大的Coder乜！以上。


