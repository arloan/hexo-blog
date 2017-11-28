title: 传说中的龙芯笔记本
id: 61
categories:
  - 技术
  - 硬件
date: 2013-12-10 18:06:16
tags:
  - 笔记本
  - 龙芯
  - linux
---

偶然在[网上](http://www.loongsonclub.cn/tg/ "龙芯笔记本团购")看到有龙芯2F笔记本1.2折团购，不含运费279块软妹币，于是想着还不到一个树莓派的价钱，还有键盘显示器，怎么想怎么划算，于是就买了一个来当玩具好了。

话说顺丰陆运也还挺快，没几天就送到了：

![loongson1](/comm-res/images/loongson1.jpg)

![loongson2](/comm-res/images/loongson2.jpg)

上面第二张左上角那个不起眼的类似学习机的就是传说中的龙芯笔记本喽。包裹送来的时候，套在笔记本外包装外面有个很厚的充气垫，发货的人还挺用心的。

启动起来桌面有个导航应用，很土的样子：

![loongson3](/comm-res/images/loongson3.jpg)

右侧有10/100M以太网卡，没有千兆比较可惜，不过也许龙芯的性能还撑不起千兆也说不定。此外还有两个USB2.0接口，一个耳机口，一个麦克口。

![loongson4](/comm-res/images/loongson4.jpg)

左侧有扇热口，电源口，VGA，一个USB2.0口和一个SD卡读卡器。总的来说，扩展性还凑合，超过了我以前用过的一个ATOM N450上网本的水平（多一个USB和一个读卡器）。

![loongson5](/comm-res/images/loongson5.jpg)

键盘有点小坑爹，很小，可能80%大小？哪位有数据的评论一下。不光小，你看~/`这个键已经安排到ESC和F1之间去了，Tab/Capslock都小的可怜，更坑爹的是右边没有Ctrl键，却有个windows右击键，这个不是一般的坑爹，作为一个MIPS64平台，只支持Linux系统，不给右Ctrl给个鼠标右键，只能说设计者的脑子被猪拱了。

![loongson6](/comm-res/images/loongson6.jpg)

自带的共创Linux下面，实际上fn+F1休眠不好使，按了跟没按一样没任何反应。

一开始不知道无线咋连，systray那里那个联网图标里右键打开无线没用，后来在网上搜索一番才知道必须fn+F5来开启硬件开关。一次可能不行，多按几次，直到笔记本C面底部右侧那个无线的LED亮了就知道打开了。

Wifi不支持802.11n，只支持11b/g，所以注意你的路由器不能设置成11n-only。

下面这个是共创Linux默认桌面应用，好（真）萌（土）~

![loongson-desktop1](/comm-res/images/loongson-desktop1.png)

![loongson-desktop2](/comm-res/images/loongson-desktop2.png)

![loongson-desktop3](/comm-res/images/loongson-desktop3.png)

![loongson-desktop4](/comm-res/images/loongson-desktop4.png)

![loongson-desktop5](/comm-res/images/loongson-desktop5.png)

看看系统信息

![loongson-sysinfo1](/comm-res/images/loongson-sysinfo1.png)

![loongson-sysinfo2](/comm-res/images/loongson-sysinfo2.png)

![loongson-sysinfo3](/comm-res/images/loongson-sysinfo3.png)

![loongson-sysinfo4](/comm-res/images/loongson-sysinfo4.png)


命令行查看CPU等资料：
```
 yee@Loong:~$ cat /proc/cpuinfo
 
 system type : lemote-yeeloong
 processor : 0
 cpu model : ICT Loongson-2 V0.3 FPU V0.1
 BogoMIPS : 528.38
 wait instruction : yes
 microsecond timers : yes
 tlb_entries : 64
 extra interrupt vector : no
 hardware watchpoint : no
 ASEs implemented :
 shadow register sets : 1
 core : 0
 VCED exceptions : not available
 VCEI exceptions : not available
 
```


```
 yee@Loong:~$ uname -a
 
 Linux Loong 2.6.27.1 #643 Sun Jan 3 11:38:33 CST 2010 mips64 GNU/Linux
 
```


```
 yee@Loong:~$ cat /proc/meminfo
 MemTotal: 1028880 kB
 MemFree: 135200 kB
 Buffers: 165440 kB
 Cached: 463552 kB
 SwapCached: 0 kB
 Active: 554288 kB
 Inactive: 270160 kB
 SwapTotal: 1024096 kB
 SwapFree: 1024096 kB
 Dirty: 272 kB
 Writeback: 0 kB
 AnonPages: 195488 kB
 Mapped: 192208 kB
 Slab: 58112 kB
 SReclaimable: 46416 kB
 SUnreclaim: 11696 kB
 PageTables: 4336 kB
 NFS_Unstable: 0 kB
 Bounce: 0 kB
 WritebackTmp: 0 kB
 CommitLimit: 1538528 kB
 Committed_AS: 432672 kB
 VmallocTotal: 137438953472 kB
 VmallocUsed: 2464 kB
 VmallocChunk: 137438950944 kB
 
```


```
 yee@Loong:~$ cat /proc/partitions
 major minor #blocks name
 
 3 0 8003520 hda
 3 1 131543 hda1
 3 2 2048256 hda2
 3 3 1024128 hda3
 3 4 1 hda4
 3 5 4799591 hda5
 
```


```
 yee@Loong:~$ sudo fdisk -l /dev/hda
 
 Disk /dev/hda: 8195 MB, 8195604480 bytes
 16 heads, 63 sectors/track, 15880 cylinders
 Units = cylinders of 1008 * 512 = 516096 bytes
 Disk identifier: 0xc9869f34
 
 Device Boot Start End Blocks Id System
 /dev/hda1 1 261 131543+ 83 Linux
 /dev/hda2 262 4325 2048256 83 Linux
 /dev/hda3 4326 6357 1024128 82 Linux swap / Solaris
 /dev/hda4 6358 15880 4799592 5 Extended
 /dev/hda5 6358 15880 4799591+ 83 Linux
 yee@Loong:~$
```

这个自带Linux版本太古老，是debian5派生版本，估计软件都没源了，改天装成debian7去。

今天就到这里喽。