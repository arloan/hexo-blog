title: unfs3在高清播放机上无法启动（续）
id: 11
categories:
  - 技术
  - 软件
date: 2013-05-05 19:24:00
tags:
  - unfs3
  - nfs
  - portmap
  - 高清播放机
---

通过samba访问高清播放机的共享目录极慢，测试了一下竟然只有可怜的5~600KBps。一开始怀疑是读取NTFS太慢，然后又测试了一下EXT4分区，速度没有分别，可见不是文件系统格式的原因。
```
~ # top
Mem: 462528K used, 5528K free, 0K shrd, 3580K buff, 151248K cached
CPU:  70% usr  23% sys   0% nic   0% idle   0% io   0% irq   5% sirq
Load average: 8.32 8.61 8.33 2/136 15558
```

从top输出来看，CPU始终处于接近100%的状态，load也非常的高，也不知道是top信息不准确还是因为高清机的主任务DvdPlayer把CPU占满了。top有问题是确定的，因为所有进程的%CPU全部报告为0，不过却不能因此认为总的CPU占用率也报告错误就是了。虽然对于单核单CPU系统来说load高得惊人，但实际上telnet上去console的反应倒是不算很慢。

CPU占用过高/load太高导致磁盘IO太慢也是可能的，因此先使用dd测试了一下NTFS分区的文件读取速度（不太在意写速度）：

```
root@localhost /innerhd/tmp
# dd if=GK-HD320.firmware.20121107.rar of=/dev/null bs=1M
163+1 records in
163+1 records out
171938398 bytes (164.0MB) copied, 7.956217 seconds, 20.6MB/s
root@localhost /innerhd/tmp
# dd if=GK-HD320.firmware.20121107.rar of=/dev/null bs=8K
20988+1 records in
20988+1 records out
171938398 bytes (164.0MB) copied, 7.028130 seconds, 23.3MB/s
root@localhost /innerhd/tmp
```

可见虽然磁盘IO在高CPU占用下速度实在不济(2xMBps)，不过也不至于慢到数百K的状况。有点惊讶的是bs=8K比bs=1M要快。看来也许确实samba太慢了，所以还是决定再折腾一下NFS。

下载到portmap5.0beta的源码后，发现是from_local.c中，find_local()函数的这个部分报错：

{% codeblock find_local() lang:c %}
	/*
     * Get list of network interfaces. We use a huge buffer to allow for the
     * presence of non-IP interfaces.
     */
    if ((sock = socket(PF_INET, SOCK_DGRAM, 0)) < 0) {
        perror("socket");
         return (0);
    }
{% endcodeblock %}

竟然是调用socket出错了。root用户运行竟然会Permission denied还真是不太可能，于是ps看了一下原来portmap会自动切换到daemon用户身份。daemon用户不能打开socket也很荒谬，也许是系统的setuid/seteuid有问题？无法确定。直接修改/etc/passwd，将daemon的uid/gid均改成0，再次运行portmap，发现其pid仍然为1，看来此路不通。

编译5.0beta运行发现其报告rpc用户不存在，fall back为bin用户，看来这个行为跟optware中的4.5不一致了。修改/etc/passwd，增加rpc用户，uid/gid为5,1，仍然一样的Permission denied。修改uid/gid为0，BINGO! unfs3成功注册了。看来只能使用5.0beta替换掉optware的4.5来用了。

退出native toolchain的编译环境，发现无法运行，因为系统的ulibc是0.9.30，编译环境是0.9.28。于是在启动脚本中加入`export LD_LIBRARY_PATH=/path/to/toolchain/lib`，搞定收工。

在笔记本上的ubuntu虚拟机中mount上测试速度：
```
$ dd if=/mnt/nfs/tmp/GK-HD320.firmware.20121107.rar of=/dev/null bs=64K
2623+1 records in
2623+1 records out
171938398 bytes (172 MB) copied, 101.602 s, 1.7 MB/s
```

不过在Windows7上mount之后，速度仍然一塌糊涂，比samba几乎没有提高，问题在哪里呢？!
