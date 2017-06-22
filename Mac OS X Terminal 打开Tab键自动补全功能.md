# Mac OS X Terminal 打开Tab键自动补全功能

打开Terminal应用程序 
输入 nano .inputrc 

再输入以下语句：

    set completion-ignore-case on
    set show-all-if-ambiguous on
    TAB:menu-complete

输入完毕后 
按下 Control ＋ o 
然后关闭、重启

