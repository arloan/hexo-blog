title: 备份龙芯笔记本的系统还原
id: 64
categories:
  - 技术
  - 硬件
date: 2013-12-11 14:33:27
tags:
  - 笔记本
  - 龙芯
  - linux
---

带共创Linux的龙芯笔记本（型号8089D）启动时有个恢复原厂系统的功能，通过此功能可以将笔记本的系统恢复到出厂时的状态，估计个人数据也一并干掉了。

因为打算把笔记本自带的系统干掉，换成Debian7，为了保险起见，决定把这个原厂系统备份一下，以免笔记本变砖。龙芯自带的存储是号称8GB SSD硬盘，但是查看当前磁盘容量时是这个样子：
```
 yee@Loong:~$ df -h
 文件系统 容量 已用 可用 已用% 挂载点
 /dev/hda5 4.6G 3.0G 1.4G 69% /
 tmpfs 503M 16K 503M 1% /lib/init/rw
 udev 503M 896K 502M 1% /dev
 tmpfs 503M 16K 503M 1% /dev/shm
 shm 503M 144K 503M 1% /tmp
```

/dev/hda只有hda5被mount了，容量为4.6G。实际上hda有4个分区，可以用fdisk看看：
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

可以看到hda有4个主分区，最后一个扩展分区划分了逻辑分区，即mount到根目录的hda5。hda1可能是用于保存boot loader，里面可见文件只有一个boot.cfg，内容为：
```
 yee@Loong:~$ cat /mnt/boot.cfg
 default 0
 showmenu 1
 
 title Cocreate Linux Desktop for Loongson
 kernel /dev/fs/ext2@wd0e/boot/vmlinux
 args console=tty no_auto_cmd quiet root=/dev/hda5 machtype=yeeloong video=smifb:1024x600-16@60 resume=/dev/hda3
```

hda3很明显是交换分区，容量为1G。这样就很明显了，那个hda2肯定就是保存的系统还原镜像一类的东西。分区格式是linux，这样可以mount起来看看，为了安全起见，还是mount成只读好了：
```
 yee@Loong:~$ sudo mount -o ro /dev/hda2 /mnt
 yee@Loong:~$ ls -al /mnt
 总计 1748616
 drwxr-xr-x 3 root root 4096 2003-01-01 .
 drwxr-xr-x 21 root root 4096 2009-12-02 ..
 -rw-r--r-- 1 root root 775352135 2004-08-12 basesys-20100706.tar.gz
 -rw-r--r-- 1 root root 174 2004-08-12 boot.cfg
 -rw-r--r-- 1 root root 1577 2003-01-01 config.txt
 -rw-r--r-- 1 root root 163205 2004-08-12 log.txt
 drwx------ 2 root root 16384 2004-08-12 lost+found
 -rw-r--r-- 1 root root 64040960 2004-08-12 OSFab-2010-0808.squashfs
 -rw-r--r-- 1 root root 942415872 2004-08-12 OSFab-thirdpart-2010-0808.squashfs
 -rw-r--r-- 1 root root 6802490 2004-08-12 vmlinuz
 yee@Loong:~$
```

很明显，这就是我要找的内容了。插个U盘备份起来就OK。找个&gt;=4G的U盘，确保只有一个分区，然后格式化成ext3:
```
 yee@Loong:~$ sudo mkfs.ext3 /dev/sdb1
 mke2fs 1.41.3 (12-Oct-2008)
 Filesystem label=
 OS type: Linux
 Block size=4096 (log=2)
 Fragment size=4096 (log=2)
 247504 inodes, 988264 blocks
 49413 blocks (5.00%) reserved for the super user
 First data block=0
 Maximum filesystem blocks=1015021568
 31 block groups
 32768 blocks per group, 32768 fragments per group
 7984 inodes per group
 Superblock backups stored on blocks:
 32768, 98304, 163840, 229376, 294912, 819200, 884736
 
 Writing inode tables: done
 Creating journal (16384 blocks):
 done
 Writing superblocks and filesystem accounting information: done
 
 This filesystem will be automatically checked every 29 mounts or
 180 days, whichever comes first. Use tune2fs -c or -i to override.
 yee@Loong:~$
```

之后把hda2的内容拷贝到U盘里就可以引导系统恢复了。可以验证以下。重启龙芯笔记本，在引导界面按DEL键，屏幕左上角会出现PMON:的提示，然后输入main，回车，进入BIOS设置。使用左右键切换到boot页签，在启动设备那里回车几次，直到选中usb0设备，然后在Exit页签那里选择重启，就可以从U盘引导进入系统恢复界面了。

![loongson-bios](/comm-res/images/loongson-bios.jpg)

![loongson-recovery](/comm-res/images/loongson-recovery.jpg)
