### origin: https://www.jianshu.com/p/050afcaf8e9c

遇到的问题：上传ipa包到appstoreconnect 一直卡在“authenticating with the app store…”

虽然之前上线Uploading这个过程运气好也差不多需要30-40分钟左右。但是如果在这个步骤卡主超过20分钟，应该就是有问题了，不要浪费时间，直接cancel掉吧。

xcode upload
Transpoter upload
电脑和Xcode的版本，都是当前最新的版本.
电脑系统：macOS Catalina 10.15.3
Xcode版本：11.3.1

Xcode11之后，在developertools里面就没有了Application Loader了,所以我这次第一开始就是直接用xcode上传包, 最后是用的Transpoter上传。

原因
1、在上传 ipa 文件时需要使用 java 程序的 iTMSTransporter 处理。
2、在第一次上传应用时，iTMSTransporter 需要下载一组 jar 文件并将其缓存在本地文件夹中。我们遇到的问题就是卡在了这一步, 所需要的文件没有下载完全。

怎么样去解决这个验证问题呢？

open /Users/${username}/Library/Caches/com.apple.amp.itmstransporter
打开这个缓存文件的文件夹，看一下文件大小，正常的大概在58M以上。如果你的文件很小，那么就需要重新下载了（建议先把/obr/2.0.0下面的jar包都删掉）。

/Applications/Xcode.app/Contents/SharedFrameworks/ContentDeliveryServices.framework/Versions/A/itms/bin/iTMSTransporter
你可以直接运行一下iTMSTransporter
或者如果你下载了Transporter
/Applications/Transporter.app/Contents/itms/bin/iTMSTransporter,这个也是一样的。

运行程序以后就会开始下载了，但是终端没有日志输出，差不多60M，就可以直接强行ctrl+c终止。

如果下载不了，可以考虑切换网络试试。

如果还是不能下载，可以从别人的电脑里面copy一份, /Users/${username}/Library/Caches/com.apple.amp.itmstransporter/obr/2.0.0/repository.xml 然后把这个文件里面的username改成你自己的。

下载好的缓存文件
-接下来，就可以直接用Transpoter上传ipa包了（进度条so的一下就到底了）。

Transpoter deliver ipa
随后，在appstoreconnect里面添加build，就能看到processing



作者：eva_lilizhang
链接：https://www.jianshu.com/p/050afcaf8e9c
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
