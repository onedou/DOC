最近搞了台阿里云给客户做服务器，但是蛋疼的GFW，让我在阿里云上npm操作举步维艰，在重试N次之后，接近崩溃边缘。

于是上网四处搜索，发现有一个cnpm的方法似乎可以解决问题，但是发现cnpm上镜像好像并不齐全，而且也是各种卡住，所以这种方式也只能放弃 ，于是搜到了一篇npm使用代理的文章，顿时醒悟。

首先，我们的npm包无所谓安全性，所以不要使用性能和效率更慢的https，转而使用http，相关命令如下：

1、关闭npm的https

    npm config set strict-ssl false

2、设置npm的获取地址

    npm config set registry "http://registry.npmjs.org/"

一般这样运气的好的话，速度就会快许多，可能会安装成功。如果你还脸黑，这样设置还是一直卡住无法下载依赖，那就只能使用proxy代理方式来解决了，命令如下：

3、设置npm获取的代理服务器地址：

    npm config set proxy=http://代理服务器ip:代理服务器端口


我就比较脸黑，最后在国外vps上加了http代理才将这些依赖全部下载下来。

希望本文能让一直无法正常下载npm而抓狂的同学有所帮助。

清除npm的代理命令如下：

    npm config delete http-proxy
    npm config delete https-proxy

最终发现cnpm其实是这么用的，我太傻了，还是cnpm靠谱

    npm install -g cnpm --registry=http://r.cnpmjs.org
    npm install microtime --registry=http://r.cnpmjs.org --disturl=http://dist.cnpmjs.org
