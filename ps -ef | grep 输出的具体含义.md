比如：

    [root@localhost ~]# ps -ef | grep ApacheJetspeed
    root     18887 18828  0 08:09 pts/0    00:00:00 grep ApacheJetspeed


字段含义如下：

    UID       PID   PPID     C STIME   TTY    TIME     CMD
    root     18887 18828   0  08:09     pts/0    00:00:00    grep ApacheJetspeed



### ps  将某个进程显示出来

-A 　显示所有程序。 

-e 　此参数的效果和指定"A"参数相同。

-f 　显示UID,PPIP,C与STIME栏位。 

### grep命令是查找

中间的|是管道命令 是指ps命令与grep同时执行

这条命令的意思是显示有关Apachejetspeed有关的进程


### UID PID PPID C STIME TTY TIME CMD 各相关信息的意义：

UID 程序被该 UID 所拥有

PID 就是这个程序的 ID 

PPID 则是其上级父程序的ID

C CPU 使用的资源百分比

STIME 系统启动时间

TTY 登入者的终端机位置

TIME 使用掉的 CPU 时间。

CMD 所下达的指令为何


对于查询结果，如何判断是运行与否呢？

    这是因为ps -ef是显示所有进程的消息，包括ApacheJetspeed和grep ApacheJetspeed这两个甚至包括ps -ef本身，而grep是查找输出包含想要的字符串的行，也就是说grep ApacheJetspeed是在所有运行的进程中查找输出包含“ApacheJetspeed”字符串的输出行，这里面就包含ApacheJetspeed，和grep ApacheJetspeed 两个进程。

即，如果运行了会显示两条输出一条是ApacheJetspeed的，令一条是grep ApacheJetspeed的。
如果没运行只会显示grep ApacheJetspeed的。
