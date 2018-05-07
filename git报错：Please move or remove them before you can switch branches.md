error: Your local changes to the following files would be overwritten by checkout:
省略中间部分
Please move or remove them before you can switch branches.

出现这个错误时：可以通过以下的命令处理：

git clean  -d  -fx ""

注： 
1. x ：表示删除忽略文件已经对git来说不识别的文件 
2. d: 删除未被添加到git的路径中的文件 
3. f: 强制执行
