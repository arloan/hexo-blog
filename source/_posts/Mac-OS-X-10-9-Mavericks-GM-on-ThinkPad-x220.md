title: Mac OS X 10.9 Mavericks GM on ThinkPad x220
id: 9
categories:
  - 技术
  - 软件
date: 2013-10-06 15:10:00
tags:
  - Hackintosh
  - Mac OS
  - Mavericks
  - x220
---

我的笔记本是ThinkPad x220, Core-i5 2410M, 4G RAM, 250G 机械硬盘。
硬盘采用的是GPT-MBR Hybrid分区，类似白苹果笔记本的Bootcamp，好处是安装苹果的时候不需要替换掉其中某个部件来支持MBR安装，不好的地方也很明显，不过对我来说不重要。

本子两年前已经折腾了一遍，安装了Mac OS X 10.7 Lion。后来一直也没升级Mountain Lion，毕竟在ThinkPad上折腾黑苹果太麻烦，升级要是失败了不想再折腾。
不过苹果家的东西，版本差太多了就不爽了，比如XCode 5就已经不支持Lion了。没办法，正好国庆放假有时间，就再折腾一下装ML。离上次折腾黑苹果已经过去了年半有余，各种知识都忘光了。进Lion里面直接运行ML 10.8.5 的升级app，重启之后没有任何反应。也许是用Chimera ISO来引导的原因？然后把InstallESD.dmg恢复到硬盘的最后一个分区，删ext，往Extra目录添ext，引导，各种崩溃。折腾了一天没成果。

然后发现10.9 GM发布了。通常GM跟正式版是一样的，于是决定折腾10.9。
稍加搜索，发现了[myHack](http://myhack.sojugarden.com/2013/06/myhack-3-2-beta-8/)这个东西。当时只有3.2 BETA8，声明只支持到Mavericks 10.9 DP2。下载，运行，指定Mavericks GM的dmg位置，许久之后，告诉我SystemVersion文件不存在。我手工mount检查了一下，这个文件明明是存在的。
想了想，手工mount之后，把InstallESD.dmg拷贝到~/Download下面，再重新运行myHack，myHack这次自己找到dmg，然后一切顺利。
重启机器，用Chameleon ISO（wowpc.iso）引导安装盘失败，白屏几秒之后直接重启了。可能是版本不够新，不支持Mavericks。myHack实际给安装盘安装了最新的Chameleon，于是通过U盘上的Grub4DOS引导安装盘的Chameleon，顺利启动。
启动之后白屏不动，重启之后用-f -v，根据屏幕提示和猜测，删除了苹果自带的PS2驱动，使用Voodoo的PS2驱动，把原系统的smbios.plist和dsdt.aml都放到Extra目录，再次重启，顺利安装完毕。PS: 发现现在已经不需要删除苹果自己的HD3000驱动了。

因为忘记格式化安装目标分区，所以实际上是从Lion升级到了Mavericks吧，系统显得不如Lion快，不知道是不是这个原因。不过好处是之前在Lion折腾的小红点什么的都正常工作，甚至发现连休眠都支持了。电池还是没有，无线网卡自然无解。有线网卡在Lion上完美，Mavericks下面竟然不正常，工作一小段时间之后DHCP总是获取不到IP。算了，反正我用USB无线网卡，不折腾了。

--------------------------------

x220有些奇怪。BIOS里面设置为legacy启动，即使硬盘是混合分区方案，MBR明明存在并且分区表合法，居然还是拒绝从硬盘启动。当然设置成efi启动是不行的，Windows不支持不说，苹果自己也不支持efi 2.0。所以从安装到使用，我一直都只能用U盘上的Grud4DOS引导Chameleon。直到一切都折腾完了，我才想起来，以前折腾Lion的时候就有过结论和解决方案，不过被我忘记了。

原因是x220的BIOS有点聪明过头。混合分区方案的第一个分区是200M的EFI分区，分区格式是0xEE。x220的BIOS似乎看到这个就认为硬盘是GPT的，不管MBR的分区表是不是合法就直接拒绝引导了。于是我只好使用WinHEX把0xEE修改成0xEC，于是，如我预期的，x220欣然从硬盘引导了。
不过，修改为0xEC之后，苹果就不认为是Hybrid分区表了，而是直接认为硬盘是MBR的。安装Mavericks的时候因为这些事情我没想起来，于是没有实施把0xEC修改为0xEE的简单做法，而是使用了gdisk这个开源软件，修复了GPT分区表，然后重建了Hybrid MBR。修复和重建工作本身很简单，但是从挖掘相关知识，寻找工具，学习如何使用等等……又花去一个晚上。
所以，我这次必须把这个结论记录下来。以上。