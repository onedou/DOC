1. find -name '*lck*' -exec rm {} \;
查找当前文件夹及其子文件夹下所有文件名中带有『lck』字符的文件并删除之。需要注意的是find -name *lck*，也就是没有加单引号，则只搜寻当前目录下的文件而不会搜索子文件夹内的文件。

顺便列一下find的相关使用
1. find   /  -name test  | xargs rm -rf      (这个命令可以查找test文件或者目录，并删除！)

2. 用下面的命令可以查找 /home下最近两天修改过的文件：find /home -type f -mtime -2

如果要把这些文件也删掉，那么可以：find /home -type f -mtime -2 -exec rm {} \;

-type f  查找文件
-type d 查找目录

-mtime -2 修改时间在2天内
-mtime +3 修改时间在3天前

-exec rm {} \;   将找到的文件 （假定找到文件的名字为 a.txt)， 执行 rm a.txt 命令

find有很多参数，有很强大的搜索功能，具体可以 man find 查看。
