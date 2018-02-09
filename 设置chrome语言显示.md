当然，如果把系统语言更改为英文，Chrome、QQ 等一系列软件会自动变成英文界面，而且没有提供切换语言的设置。

啪了下 Google，找到了方法，直接在终端运行后重启 Chrome 即可更改。

英文 -> 简体中文

defaults write com.google.Chrome AppleLanguages '(zh-CN)'
1
简体中文 -> 英文

defaults write com.google.Chrome AppleLanguages '(en-US)'
1
英文优先，简体中文第二。反之改一下顺序

defaults write com.google.Chrome AppleLanguages "(en-US,zh-CN)"
