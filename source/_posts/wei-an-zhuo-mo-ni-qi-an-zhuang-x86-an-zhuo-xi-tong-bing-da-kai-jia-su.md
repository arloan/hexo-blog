title: 为安卓模拟器安装x86安卓系统并打开加速
id: 89
categories:
  - 技术
  - 软件
date: 2013-12-26 16:44:16
tags:
  - Android
  - 模拟器
---

安卓平台的模拟器性能很差，这一点一直让安卓开发人员深恶痛绝，以至于长时间以来，安卓开发人员都使用真机进行开发调试，模拟器简直成了摆设。模拟器性能低下的主要原因之一是模拟器中的安卓仍然是运行于arm平台，而模拟器主机则都是x86/x64，因此模拟器需要进行指令集转换，CPU的执行效率大打折扣。然而即使在安卓发布了x86版本的系统之后，我们发现其运行在模拟器中的速度也仍然不尽人意，这不禁让人开始怀疑安卓团队在模拟器方面的技术水平问题了。
直到Intel发布了HAXM（Hardware Accelerated eXecution Manager）之后，情况才得以改观，经过实践，发现安装了HAXM并打开GPU加速之后，x86版的安卓在模拟器中终于可以流畅的运行了。

首先我们打开Android SDK Mananger，下载安装x86的安卓操作系统镜像（这里以4.2.2为例，4.4我还没下下来）：

![android-x86-image](/comm-res/images/android-x86-image.png)

然后下载“Extra”目录下的Intel x86 Emulator Accelerator (HAXM) 软件包，如下图：
![android-haxm](/comm-res/images/android-haxm.png)

下载完之后Android SDK Manager会显示HAXM是“Installed“，你可能以为这样就OK了，其实你被骗了，实际上还需要手工安装一下这个HAXM。进入到你的安卓SDK目录，在`Extra/intel/Hardware_Accelerated_Execution_Manager`目录下有HAXM的安装包，苹果系统下是这样的：

![android-haxm2](/comm-res/images/android-haxm2.png)

双击IntelHAXM.dmg挂载磁盘镜像，然后双击其根目录下的.mpkg安装包，一路按照提示装完即可。

Windows下面是个IntelHaxm.exe的安装包，双击运行，类似以下：

![android-haxm3](/comm-res/images/android-haxm3.png)

这个图是借的，手头没有Windows，现在的版本应该是1.0.6了。当然在安装的时候可以设置一下HAXM使用的内存，尤其你只有可怜的4G内存，而Eclipse又是内存大户：

![android-haxm4](/comm-res/images/android-haxm4.png)

这个内存是给所有x86安卓模拟器共用的，如果你总是只跑一个模拟器，完全可以改小点。之后如果想重新设置内存大小，那么需要重新运行一遍HAXM的安装程序，而且需要关闭所有x86的安卓模拟器。
此外，Intel官方说 1.0.6r3的HAXM会和VirtualBox 4.2.14及更新版冲突，导致模拟器冻结，不过我现在用的是VirtualBox 4.2.6，所以没有验证这个情况。

装好HAXM之后，我们来创建一个x86的安卓虚拟设备。打开Android Virtual Device Manager，新建一个虚拟设备，关键的地方是要在CPU/ABI那里选择x86，以及在Emulation Options那里勾选Use Host GPU：

![android-avd](/comm-res/images/android-avd.png)

保存之后，在Eclipse里右键安卓工程，Run As-&gt;Android Application，选择你刚创建的x86虚拟设备，应当就启动了模拟器：

![android-avd2](/comm-res/images/android-avd2.png)

启动过程中主要到Eclipse的Log 窗口有下面红色那一句就说明安装配置正确：

![android-log](/comm-res/images/android-log.png)

就是这句：<span style="color: #ff0000;">**Emulator] HAX is working and emulator runs in fast virt mode**</span>
