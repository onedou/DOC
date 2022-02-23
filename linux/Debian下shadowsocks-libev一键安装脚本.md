#Debian下shadowsocks-libev一键安装脚本

![](https://github.com/onedou/DOC/blob/master/Images/201609020957.png)

**本脚本适用环境：**  
**系统支持：Debian/Ubuntu**  
**内存要求：≥128M**  
**日期：2016 年 08 月 29 日**  

>关于本脚本：

Debian 或 Ubuntu 下一键安装 libev 版的 shadowsocks 最新版本。该版本的特点是内存占用小（600k左右），使用 libev 和 C 编写，低 CPU 消耗，甚至可以安装在基于 OpenWRT 的路由器上。  
友情提示：如果你有问题，请先参考这篇《Shadowsocks Troubleshooting》后再问。

>默认配置：

**服务器端口：自己设定（如不设定，默认为 8989）**  
**客户端端口：1080**  
**密码：自己设定（如不设定，默认为teddysun.com）**

>客户端下载：

**https://github.com/shadowsocks/shadowsocks-windows/releases**

>使用方法：

使用root用户登录，运行以下命令：

	wget --no-check-certificate https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-libev-debian.sh
	chmod +x shadowsocks-libev-debian.sh
	./shadowsocks-libev-debian.sh 2>&1 | tee shadowsocks-libev-debian.log

安装完成后，脚本提示如下：

	Congratulations, shadowsocks-libev install completed!
	Your Server IP:your_server_ip
	Your Server Port:your_server_port
	Your Password:your_password
	Your Local IP:127.0.0.1
	Your Local Port:1080
	Your Encryption Method:aes-256-cfb

	Welcome to visit:https://teddysun.com/358.html
	Enjoy it!

>卸载方法：

使用 root 用户登录，运行以下命令：

	./shadowsocks-libev-debian.sh uninstall

>其他事项：

客户端配置的参考链接：https://teddysun.com/339.html  
本脚本安装完成后，已将 shadowsocks-libev 加入开机自启动。

>使用命令：

启动：/etc/init.d/shadowsocks start  
停止：/etc/init.d/shadowsocks stop  
重启：/etc/init.d/shadowsocks restart  
状态：/etc/init.d/shadowsocks status  

[https://teddysun.com/358.html](https://teddysun.com/358.html)

