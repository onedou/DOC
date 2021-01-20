# vsftp支持TLS加密传输的配置说明

origin: https://www.sysit.cn/blog/post/sysit/vsftp%E6%94%AF%E6%8C%81TLS%E5%8A%A0%E5%AF%86%E4%BC%A0%E8%BE%93%E7%9A%84%E9%85%8D%E7%BD%AE%E8%AF%B4%E6%98%8E

## 1. ftp传输存在的问题

因为数据传输的需要，对部门或者外部客户开放的FTP越来越多，但是FTP本身的传输数据是明文的。可以通过抓包工具来分析到账号和密码，迫切需要一个更加安全的FTP服务器。因此考虑配合SSL来解决这个问题。

通过tcpdump抓包如下图：

![avatar](https://www.sysit.cn/api/file/getImage?fileId=5e168406e0dda60e8500075e)

上图中可以清楚地看到FTP的账号密码以及上传的文件名。

## 2. 关于ssl说明

SSL(Secure Socket Layer)工作于传输层和应用程序之间。作为一个中间层，应用程序只要采用SSL提供的一套SSL套接字API来替换标准的Socket套接字，就可以把程序转换为SSL化的安全网络程序，在传输过程中将由SSL协议实现数据机密性和完整性的保证。Ftp结合SSL,将实现传输数据的加密,保证数据不被别人窃取。

## 3. vsftpd配置ssl

查看vsftpd是否支持ssl
```
[root@node5 ~]# ldd /usr/sbin/vsftpd |grep libssl
        libssl.so.10 => /lib64/libssl.so.10 (0x00007fc8de165000)
```

生成ssl

```
[root@node5 ~]# openssl req -newkey rsa:2048 -nodes -keyout /etc/vsftpd/vsftpd.key -x509 -days 365 -out /etc/vsftpd/vsftpd.pem -subj "/C=CN/ST=SC/L=CD/O=SYSIT/OU=sa/CN=sysit.cn/emailAddress=sa@sysit.cn"
Generating a 2048 bit RSA private key
.............................................................+++
..................................................................................................+++
writing new private key to '/etc/vsftpd/vsftpd.key'
-----
```

- 配置vsftpd

```
ssl_enable=YES
allow_anon_ssl=NO
force_local_data_ssl=YES
force_local_logins_ssl=YES
force_anon_logins_ssl=YES
force_anon_data_ssl=YES
ssl_tlsv1=YES
ssl_sslv2=NO
ssl_sslv3=NO
require_ssl_reuse=NO
ssl_ciphers=HIGH
rsa_cert_file=/etc/vsftpd/vsftpd.pem
rsa_private_key_file=/etc/vsftpd/vsftpd.key
ssl_ciphers：加密方法，默认是DES-CBC3-SHA，测试发现filezilla需要HIGH。
require_ssl_reuse：默认值是YES，需要禁用它。
ssl_tlsv1：tlsv1连接是首选。
```

非加密登录报错
如下，启用了tls之后，就不能再用非加密方式登录了，报错如下：

```
[admin@node4 ~]$ ftp 10.50.101.14
Connected to 10.50.101.14 (10.50.101.14).
220 (vsFTPd 3.0.2)
Name (10.50.101.14:admin): ftpuser
530 Non-anonymous sessions must use encryption.
Login failed.
421 Service not available, remote server has closed connection
```

- 加密连接

这时我们就需要诸如FileZilla、WinSCP等支持SSL/TLS的工具来连接。例如：这里使用WinSCP连接，如下图：

![](https://www.sysit.cn/api/file/getImage?fileId=5e168acfe0dda60e8500075f)

连接类型：外部TLS/SSL加密

![](https://www.sysit.cn/api/file/getImage?fileId=5e168ae8e0dda60e85000760)

再次抓包
发现已经被加密了。

![](https://www.sysit.cn/api/file/getImage?fileId=5e168c51e0dda60e85000761)
