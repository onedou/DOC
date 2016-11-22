# 为git设置HTTPS代理

&emsp;&emsp;距离5月35日还有一周的时间，政府就开始提醒我们这一天的存在了。与 GitLab 齐名的免费代码仓库 Bitbucket 不幸躺枪，pull 和 push 的通道都被封锁，取而代之的是这样一条消息：

    fatal: unable to access xxx: Failed connect to bitbucket.org: 443 ; No error

&emsp;&emsp;然而一个程序员绝不能以网络不畅为由停止手中的工作，只要为 git 设置 HTTP/HTTPS 代理，就可以化解上面的问题。

&emsp;&emsp;为 git 设置 HTTP/HTTPS 代理的详细方法，可以在 StackOverFlow 上找到。对于大多数天朝非 VPN 用户而言，常用的科学上网方法大多是使用某门，某风，以及 SS. 他们的共同特点是需要安装一个客户端，在本地的某个端口监听流量，通过服务器转发该端口的数据包。在这种情况下，设置代理的方式会变得极为简单。假设客户端监听的端口号是3456，只需在控制台中输入如下指令即可：

    git config --global http.proxy  http://localhost:3456
    git config --global https.proxy https://localhost:3456

&emsp;&emsp;对 git 有一定了解的读者会知道，git 的全局配置文件存放在用户的主目录下。对于 Windows 7 以上的系统，就是 C:\Users\USERNAME\.gitconfig . 使用文本编辑器打开，就可以看到刚刚写入的设置了。

    [http]
    proxy = http://localhost:3456

    [https]
    proxy = https://localhost:3456

&emsp;&emsp;因此，如果哪天不需要使用代理了，可以不必如 StackOverFlow 所述使用 unset 指令，而是将上面的四行设置直接删除即可。如果你还有兴趣，可以尝试用 # 或者 ; 把相关的语句注释掉，看是否行得通。不过 .gitconfig 毕竟是一个自动生成的文件，在上面大动手脚说不定是会出问题的。
