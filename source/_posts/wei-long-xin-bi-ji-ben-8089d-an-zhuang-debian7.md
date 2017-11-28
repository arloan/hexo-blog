title: 为龙芯笔记本8089D安装Debian7
id: 83
categories:
  - 技术
  - 软件
date: 2013-12-26 15:05:46
tags:
  - linux
  - debian
  - 笔记本
  - 龙芯
---

龙芯笔记本自带的Linux是基于Debian5的，有点太老了，决定装个debian7。

[这里](http://d-i.debian.org/daily-images/mipsel/daily/loongson-2f/netboot/ "debian loongson net-boot kernel daily build")是debian官方龙芯2f网络启动安装kernel文件，最新的是2013/12/15生成的。不过我用这个版本安装虽然能成功，但是貌似无线网卡驱动有问题，把wifi起起来会kernel panic，所以我又换了个确定wifi没问题的比较老点的版本：3.2.0-4的kernel，在[这里](http://ftp.cn.debian.org/debian/dists/wheezy/main/installer-mipsel/current/images/loongson-2f/netboot/ "debian loongson-2f 3.2.0-4 kernel")有国内的镜像。

启动需要3个文件：boot.cfg, initrd.gz和vmlinuz-3.2.0-4-loognson-2f。准备一个U盘，确保只有一个主分区，使用linux格式化成ext2/ext3，然后将前面的3个文件拷贝进去，启动盘就做好了。

实际上我使用了上一篇博文中备份龙芯系统恢复U盘。那个U盘里面已经有一个boot.cfg，当然不要覆盖掉，拷贝的时候把debian7启动盘的boot.cfg换个名字就好，比如boot-deb7.cfg，这样自带系统恢复盘和debian安装盘就可以共用一个U盘了。

把U盘插入8089D左侧的USB口（其他USB口是否可以启动不知道，我没试过），重启笔记本，在启动画面按下DEL键，应该会出现 PMON&gt; 的提示符。当然我安装的时候，因为早先已经把BIOS设置里该成U盘启动，而这个U盘本身有系统恢复盘的boot.cfg，所以直接就出现了boot.cfg的启动菜单。不用着急，这时候按下C键就进入了命令行。

在PMON命令行输入devls，看看是否有usb0设备，如果没有，说明没认到U盘，也许是兼容性问题，换一个U盘试试吧。

如果认到了之后，输入以下命令加载安装盘的启动文件：
`bl -d ide (usb0,0)/boot.cfg`

当然，我的启动配置文件已经重命名成boot-deb7.cfg，所以我安装的时候实际使用的命令行自然是
`bl -d ide (usb0,0)/boot-deb7.cfg`

注意bl是BL的小写字母，不是123的1，-d前面和ide前后都有个空格。
这个时候就应该能显示启动菜单，只有一项，回车开始安装。回车后可能就黑屏了，放心，就是龙芯慢而已，等个几分钟就进入了安装过程，跟x86/x64的debian安装过程没什么区别。

配置网络的时候我用的有线，无线在第一次使用最新的debian内核安装时没有成功，所以重新安装的时候也没再试。要用无线的话，先要fn+F5，然后选中wifi回车。如果正常，笔记本右下侧的wifi灯应该亮起来。注意fn+F5是开关切换键，所以按一次就行了，再按会又关掉无线。再就是8089D的无线只支持802.11b/g，所以11n-only的路由是连不上的。

选安装软件的时候只选最后三项：SSH server, laptop和标准系统工具。图形界面的话不要现在选，否则起不起来。
然后就没什么特殊的了，最后装完重启就一切OK了。

到目前为止我还没有去安装图形系统，也没这打算。哪天有心思了再折腾下LXDE看。
