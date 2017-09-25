原文地址：
https://blessing.studio/use-ssh-tunel-to-map-a-lan-minecraft-server-to-wan/

这个标题到底是什么意思呢？首先来了解一下如下概念吧：

## 内网 Minecraft 服务器

所谓内网 Minecraft 服务器，就是架设在内网机器上的 MC 服务器，这些机器的最大特点，那就是**不能从外网直接访问**。

不能直接访问的原因有很多，最大的原因就是**没有公网 IP，经过多级 NAT，没有修改 NAT 设备（例如路由器）配置的权限**之类。举个栗子，就像校园网，公司网络这样的。不同于普通家庭 PC 开服，校园网完全访问不到上级 NAT 设备，更不要提什么配置端口转发之类的了，所以很多人就这样放弃了开服，或者使用**花生壳**，**NAT123** 之类的 “端口映射” 软件。

那么这类软件的原理是什么呢？

## 花生壳端口映射的原理

用过所谓**花生壳内网版**的都知道，开启了端口映射后，你就可以从花生壳那里拿到一个类似于**diaoxiancheng.6655.la:24000**这样的地址，访问这个地址就相当于直接访问内网机器上的端口了。

不是说不能端口映射吗？花生壳这啥黑科技？

首先你要知道，所谓的路由器上设置端口映射，其实就是**将外网访问 NAT 设备某个端口的请求，直接转发给规则中配置的 NAT 网络下的内网设备端口上**。不知道说的是不是够严谨，总之差不多就是这个意思。实现方式有很多，譬如 OpenWRT 上使用的 iptables。

而花生壳，就是把**NAT 设备将请求转发到内网机器上**这一过程及其反向，用花生壳的服务器来完成了。

这样说说可能有些人还是一头雾水，窝画张图来解释下吧：（提到画图歪一下文，安利下 Gliffy，贼好用）

![image](https://user-images.githubusercontent.com/3422640/30767856-6d2bf78a-a033-11e7-8504-01a85dec8601.png)

这是普通的路由器端口映射原理，下面是花生壳 “端口映射” 原理：

![image](https://user-images.githubusercontent.com/3422640/30767870-8e9e62ea-a033-11e7-90ee-a3e485216416.png)

是不是很熟悉呢？没错，就是翻墙的原理。  
花生壳用它自己的服务器与内网机器建立**TCP 连接（穿过 NAT）**，监听内网机器本地端口，将数据包转发至花生壳服务器，花生壳服务器再将数据包转发至客户端。也就是类似于代理服务器。

那么知道了原理，我们是不是可以自己实现一个呢？

## SSH 端口转发（隧道）

SSH，Secure Shell，是一项创建在应用层和传输层基础上的安全协议，为计算机上的 Shell（壳层）提供安全的传输和使用环境。
稍微会一些运维的人都用过 ssh 连接过远程主机吧，但 ssh 还有一个非常屌的功能 **隧道**。

不过你只需要知道它可以在内网机器和外网机器之间建立一个连接并且传输数据就可以了。

其实严谨的讲，不应该将 SSH 端口转发（Port Forwarding）称作 隧道（Tunel），在 ssh 的 man page 里也有写，算是普遍的误解了，所以将错就错吧。

关于 SSH 端口转发的更多信息，详见[Linux Wiki：SSH端口转发（隧道）](http://linux-wiki.cn/wiki/zh-hans/SSH%E7%AB%AF%E5%8F%A3%E8%BD%AC%E5%8F%91%EF%BC%88%E9%9A%A7%E9%81%93%EF%BC%89)

在 MCBBS 上使用[Windows Server VPN 做端口转发](http://www.mcbbs.net/thread-495758-1-1.html)的教程，基本原理都是一样的。但是配置过程实在是太不优雅了，使用 ssh -R 的话，就是一行的事儿。

## 配置SERVER端

知道了这些基本概念，那么就开始配置吧~

### 准备材料：

- 可用的，低延迟的国内VPS一台
- 开服用的计算机
- 足够的带宽
- 不畏折腾的心


### 开启 Minecraft 服务器

这里用最新版快照的官服做演示（话说官服加载真快啊）

![image](https://user-images.githubusercontent.com/3422640/30788558-82d8dc3e-a163-11e7-8745-91cbd6c4192d.png)

### Linux 环境下的配置

首先来看看 ssh 的 man page。  
这永远是了解一个命令的最好方法。

    man ssh
    
![image](https://user-images.githubusercontent.com/3422640/30788615-4dd82232-a164-11e7-86f6-8d257b3736d6.png)

那么，来建立连接吧。在本地打开终端，输入：

    ssh -R 0.0.0.0:25567:localhost:25565 root@your-server-ip

![image](https://user-images.githubusercontent.com/3422640/30788635-763728d6-a164-11e7-9632-996cc7f37394.png)

在输入密码后进入的VPS shell上执行**netstat -anp**，是不是看到sshd已经在监听25567了？
（虽然你看到的应该是监听在 127.0.0.1 上，具体等下讲）

那么这行命令是什么意思呢？  

这条命令的格式是这样的：

    ssh -R [bind_address:]port:host:hostport

意思是让远程服务器监听port端口，使其被访问时像本地电脑在访问 host:hostport 一样。

    ssh -R 0.0.0.0:25567:localhost:25565 root@your-server-ip

这样，访问**your-server-ip:25567就等同于用本机访问localhost:25565一样**。

通俗地讲，就是将**本机 25565**端口映射至**VPS 25567**端口。

那么bind_address的0.0.0.0是什么呢？ 

因为远程转发的端口默认也只能在远程服务器本机上访问（即 localhost 等本地环回地址）。 
要想允许外部访问，就要将 bind_address 设为 * 或者 0.0.0.0。

> 确保在服务器的sshd_config中打开了 GatewayPorts 选项。

没有设置GatewayPorts可能会导致在 VPS 上显示监听在 127.0.0.1 上，而且 MC 客户端无法连接。

那么来看一下 GatewayPorts 的 man page 吧：

![image](https://user-images.githubusercontent.com/3422640/30788789-11ccb2d8-a166-11e7-895b-1ea7d9c0e458.png)

总之只有打开了这个，才能在外网访问 VPS 所转发的端口。

在 VPS 上，编辑 /etc/ssh/sshd_config，将其中的 GatewayPorts 设置为 on。

![image](https://user-images.githubusercontent.com/3422640/30788803-37a9589e-a166-11e7-835a-efd64a34d6ff.png)

保存后，重载sshd，再次执行ssh命令，是不是发现端口已经监听在 0.0.0.0 上了呢？  
重置sshd：
    
    service sshd reload

MC 客户端可以正常访问。

### 小技巧：

像这样 ssh -R，输完密码（或密钥认证）后，会直接进入 VPS 的 Shell，但是只想转发某个端口，要怎么办呢？

    -f 认证之后，ssh 将自动转至后台运行  
    -N 不执行脚本或命令，即通知 sshd 不运行设定的 shell  
    -C 压缩传送的数据  

![image](https://user-images.githubusercontent.com/3422640/30788857-d1116648-a166-11e7-9914-6712379d74f2.png)

Linux 下的端口转发配置这样就算结束了，是不是很简单呢~

### Windows 下的配置吧。

其实 Win 下用 Cygwin/Babun 这些在终端里打 ssh -R 照样能建立连接，但是看这里的一般都想有个 GUI 吧？

使用GUI的PuTTY

![image](https://user-images.githubusercontent.com/3422640/30788921-3a16928a-a167-11e7-861a-82e671078229.png)

然后进 SSH – Tunel：

![image](https://user-images.githubusercontent.com/3422640/30788933-53513de0-a167-11e7-9478-eea9c13ef9ee.png)

最后点 Add 按钮添加规则，去 Session 页，保存成一个配置集，以后就可以直接 Open 了。

![image](https://user-images.githubusercontent.com/3422640/30788947-776f5d7e-a167-11e7-80c5-603700b10e4b.png)

至此，你已完成 ssh 端口转发的配置。

