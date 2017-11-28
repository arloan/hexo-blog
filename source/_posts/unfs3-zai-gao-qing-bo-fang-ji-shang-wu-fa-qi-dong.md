title: unfs3在高清播放机上无法启动
id: 15
categories:
  - 技术
  - 软件
date: 2013-04-21 15:26:00
tags:
  - unfs3
  - nfs
  - portmap
  - 高清播放机
---

在高清播放机开博尔K660I上开启了telnetd，使用telnet客户端登录，安装了[optware](http://www.nslu2-linux.org/wiki/Optware/)之后，试图开启nfs服务，将播放器内置的硬盘共享给PC和家庭网络上的其他设备。之所以选择NFS服务是因为Linux的samba服务比较低效，性能不如NFS。

我安装的optware中仅提供了[unfs3](http://unfs3.sourceforge.net/)这个用户态NFS服务器。使用以下命令安装：
`ipkg install unfs3 portmap`
安装完成之后，编辑`/opt/etc/exports`，写入以下内容之后保存：
`/tmp/usbmounts  192.168.1.0/24(rw,anonuid=0,anongid=0,all_squash)`
此处假设家庭网络的网段为192.168.1.0/24，播放器将所有内置硬盘和外置USB存储设备均被mount在`/tmp/usbmounts`下。

之后先启动portmap：`/opt/sbin/portmap`
然后启动unfs3：`/opt/sbin/unfsd -e /opt/etc/exports`
unfs3启动失败，报告：

```
~ # unfsd -e /opt/etc/exports
unable to register (NFS3_PROGRAM, NFS_V3, udp).
```

杀掉portmap，然后以调试+冗余模式启动到console：
`/opt/sbin/portmap -dv`
再次启动unfs3，查看portmap的输出，显示以下错误信息：
```
~/home/root # portmap -dv
server: about do a switch
portmap[21891]: socket: Permission denied
portmap[21891]: cannot find any active local network interfaces
server: about do a switch
portmap[21893]: connect from 127.0.0.1 to unset(100003): request from non-local host
portmap[21891]: socket: Permission denied
portmap[21891]: cannot find any active local network interfaces
portmap[21894]: connect from 127.0.0.1 to set(100003): request from non-local host
server: about do a switch
portmap[21891]: socket: Permission denied
portmap[21891]: cannot find any active local network interfaces
portmap[21895]: connect from 127.0.0.1 to unset(100003): request from non-local host
```

如果使用-p启动unfsd，禁止向portmap注册端口，倒是可以启动成功，但是客户端无法mount。使用ubuntu试图mount得到以下错误：
```
$ sudo mount 192.168.1.62:/tmp/usbmounts /mnt
[sudo] password for xxxx:
mount: wrong fs type, bad option, bad superblock on 192.168.1.62:/tmp/usbmounts,
       missing codepage or helper program, or other error
       (for several filesystems (e.g. nfs, cifs) you might
       need a /sbin/mount.<type> helper program)
       In some cases useful info is found in syslog - try 
       dmesg | tail  or so
```

然而/var/log/syslog和dmesg没有任何相关日志。
