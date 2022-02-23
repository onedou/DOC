sslocal -c ss.json


用pip安装就很简单。
1. 安装ss
apt-get install python-pip & pip install shadowsocks
2. 启动 ss客户端
启动方法a，直接在终端用命令：
sslocal -s 123.123.213.213 -p 6666 -b 127.0.0.1 -l 1080 -k 23333 -t 600 -m aes-256-cfb把ss启动命令写成shell脚本方便使用。
启动方法b，用配置文件启动：
配置文件存为ss.conf，格式
 {
"server" : "123.123.213.213",
"server_port" : 6666,
"local_port" : 1080,
"password" : "23333",
"timeout" : 600,
"method" : "aes-256-cfb"
}启动时使用命令：
sslocal -c /filepath/to/ss.conf
完成。
PS：
a.记得在Network设置代理: 设置Socks Host指向 ss客户端的本地IP和端口, 即127.0.0.1 1080;
b.有同学反应还是不能科学上网。说明一下，SS不同于VPN，它是走socks5协议的，一般搭配浏览器食用，对于terminal的get,wget等走http是没有帮助的。虽然有socks转http的方法，但这里就不折腾了。
=====UPDATE=====
3. 开机启动ss(可选)
在/etc/rc.local中添加启动命令。
例如:
sudo  vi /etc/rc.local在exit 0前添加(这里假设你已经在第2步写好shell脚本，并命名为ss_start.sh)
sudo sh /path/to/sslocal/ss_start.sh如果路径和权限都没问题，在下次开机时就会启动ss了。
查看ss是否已经开启，用下面这个:
ps -ef | grep sslocal

----------------------------------------------------------------------
sudo apt-get install privoxy
Privoxy的配置文件在/etc/privoxy/config

找到4.1. listen-address这一节，确认监听的端口号。
找到5.2. forward-socks4, forward-socks4a, forward-socks5 and forward-socks5t这一节，加上如下配置，注意最后的点号。
sudo /etc/init.d/privoxy restart
