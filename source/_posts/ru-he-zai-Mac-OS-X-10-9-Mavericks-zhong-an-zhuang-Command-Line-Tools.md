title: 如何在Mac OS X 10.9 Mavericks 中安装 Command Line Tools
id: 8
categories:
  - 技术
  - 软件
date: 2013-10-09 22:52:00
tags:
  - Mac OS
  - Mavericks
  - XCode
---

XCode5 on Mac OS X 10.9 Mavericks中，在Preferences -&gt; Downloads 中已经没有 command line tools 的下载项目：
![](/comm-res/images/XCode-cmdtools1.png)
实际上，在Mavericks里面，现在可以直接安装 command line tools 而不再需要安装 XCode 了。

安装 command line tools 的办法：
打开一个shell窗口，输入xcode-select --install，回车：
![](/comm-res/images/XCode-cmdtools2.png)
系统会跳出窗口，询问是否安装command line tools：

![Screen Shot 2013-12-10 at 4.32.07 PM](/comm-res/images/XCode-cmdtools3.png)

选择 "install" 即可安装命令行编译工具：

![Screen Shot 2013-12-10 at 4.32.43 PM](/comm-res/images/XCode-cmdtools4.png)

![Screen Shot 2013-12-10 at 4.33.02 PM](/comm-res/images/XCode-cmdtools5.png)
