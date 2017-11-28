title: 让自己的USB设备免于沦为传播病毒的工具！
id: 24
categories:
  - 技术
  - 软件
date: 2006-10-10 22:07:00
tags:
  - Windows
  - USB
  - 自动运行
  - 病毒
---

由于USB接口的存储设备的流行（如U盘，USB硬盘，MP3等），病毒木马的作者们开始利用Windows的自动运行功能，让这些移动设备渐渐沦传播这些害人软件的帮凶。那么如何避免被害和为虎作伥呢？

为了达到这个目的，需要做两件事情。一，让自己的移动设备免于被此类病毒感染；二，即使有被感染的移动设备接到我的机器上，也不会让病毒有机会运行。

实现第一个目标很简单。这类病毒都是利用移动设备根目录下的autorun.inf传播的，因此，避免被感染的一个办法就是预先生成autorun.inf。然而只是创建一个autorun.inf文件的话，会被病毒覆盖掉，所以，最好的办法是创建一个名为autorun.inf的**文件夹**。为了省得碍眼，可以把这个文件夹设置成隐藏的。当然，如果你的移动设备已经感染了，那么在这么做之前至少得先删除掉autorun.inf。这个文件通常是隐藏的，在默认配制的windows下看不见。最好的方法是用命令行。假设你的移动设备驱动器是X盘，那么开始->运行->输入CMD，回车，在出现的黑色命令行窗口中输入以下命令，然后回车：
```
attrib -s -h -r X:\autorun.inf & del /f /q X:\autorun.inf & mkdir X:\autorun.inf
```

至于第二个目标，解决办法就是彻底关掉Windows的自动运行功能。这在有些人看来可能觉得使用不够方便了，不过有得就有失嘛，自动运行在我看来意义并不大。如何关掉自动运行能？方法之一是使用策略管理器。开始->运行：输入gpedit.msc，回车，就可以打开策略管理器。依次展开左侧的Local Computer Policy (本地计算机策略) -> Computer Configuration (计算机设置) -> Administrative Templates (管理员模板) -> System (系统)，然后选中 System 节点，双击右侧的 Turn off Autoplay (关闭自动运行)条目，然后在弹出的对话框中选择Enabled (允许)，并在下面的下拉框中选择 All drives(所有驱动器)，确定即可。因为我的系统是英文的，所以上面的翻译可能与中文系统中的描述有所差异。另外，根据我的经验，似乎Windows XP需要注销或者重起才能生效？Windows 2000/2003似乎是立即生效的。

![](/comm-res/images/USB-autoplay-1.png)
![](/comm-res/images/USB-autoplay-2.png)