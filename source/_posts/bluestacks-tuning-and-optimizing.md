title: Bluestacks 的调整与优化
categories:
  - 技术
  - 软件
date: 2015-08-29 13:07:11
tags:
  - Android
  - Bluestacks
---

<!-- toc -->

### 前言
[Bluestacks](http://www.bluestacks.com) 是一个在 PC 和 Mac OS 上免费的安卓模拟器，目的是为了让用户在 Windows 上玩安卓游戏，当然，跑安卓应用也是没问题的。说它是个模拟器其实并不完全正确，因为它使用的是 x86 的安卓核心，因此实际上它是一个安卓虚拟机。考虑到 PC 的屏幕方向，我们更多的是把它当作一个安卓平板来使用。Bluestacks 的运行速度很快，也可以当作安卓开发测试的安卓设备来使用，比 Google 官方的安卓模拟器无论是性能方面还是便利性方面都强太多了。

然而安装完 Bluestacks 之后，它并没有提供任何的可选项调整界面，官网上也几乎都没有任何说明，本文所有调整内容均需要直接操作 Windows 的注册表，请小白自行评估风险，作者概不负责。

PS. 在国内访问其[主页](http://www.bluestacks.com)始终会重定向到[中文版](http://www.bluestacks.cn)。

### 修改内存使用
默认的情况下，Bluestacks 安装时会根据系统内存大小，自行决定安卓虚拟机的内存使用量。例如在我的 16GB Ram Windows 7 上，装完之后其内存上限设定为 1856M。修改方法是，打开注册表编辑器，定位到 `HKEY_LOCAL_MACHINE\SOFTWARE\BlueStacks\Guests\Android`，在右侧窗口修改 `Memory` 条目即可。注意你用的是十进制还是16进制，别搞错了。修改完毕之后，需要重启 Bluestacks 生效。

### 打开 VT-x 特性
VT-x 是 intel 运用 Virtualization 虚拟化技术中的一个指令集，是CPU的硬件虚拟化技术，可以同时提升虚拟化效率和虚拟机的灵活性、稳定性。由于这个技术不是所有CPU型号都支持，所以 Bluestacks 默认是不开的。

打开注册表编辑器，定位到 `HKEY_LOCAL_MACHINE\SOFTWARE\BlueStacks\Guests\Android`，在右侧窗口中修改 `VirtType` 的值为 `vtx`，然后再定位到 `HKEY_LOCAL_MACHINE\SOFTWARE\BlueStacks\Guests\Android\Config`，修改右侧窗口中 `ForceVMLegacyMode` 的值为 0 即可。当然，修改完毕之后，需要重启 Bluestacks。

在我的 SandyBridge i5-2410M 上，打开 VT-x 之后，性能有比较明显的提升。

### 修改虚拟机窗口大小和安卓分辨率和色深
如果你想让 Bluestacks 在全屏和窗口化之间切换的话，按 F11 就可以了。但是默认的窗口比较小，如果你拿来跑安卓应用为主的话，应该是窗口化比较方便，于是就会希望把窗口调大一点。

打开注册表编辑器，定位到 `HKEY_LOCAL_MACHINE\SOFTWARE\BlueStacks\Guests\Android\FrameBuffer\0`，右侧窗口中有几个值可以调整：
- **Depth**: 这个是色深，默认16bit，可以试试改成32bit，不过我没改啦。
- **FullScreen**: 顾名思义，不过有 F11，就不必改它了。
- **GuestHeight** 和 **GuestWidth**: 这个是安卓系统的分辨率。如果你主要使用窗口化，那么建议设置为跟 Bluestacks 的窗口大小一致，这样安卓系统画面就不需要插值，比较清晰。主要全屏用的话，可以设置成你显示器的物理分辨率，这样全屏的时候最清晰。
- **WindowHeight** 和 **WindowWidth**: 这个是窗口化之后，Bluestacks 窗口的大小。注意，这个大小不是窗口的 FrameSize，而是 ClientSize 噢！也就是内容大小，不包含窗口边框（但是毫无疑问的包含了那个安卓虚拟按键条部分）。

### 安卓与 Windows 之间的文件交换
Bluestacks 默认就设置了几个与 Windows 共享的目录，安卓下的路径位于 `/sdcard/windows` 下面：
![shared folders](/comm-res/images/bluestacks-shared-folders.png)
- **BstSharedFolder**: 在 Windows 中的位置是 `%bluestack-data-dir%BlueStacks\UserData\SharedFolder`
- **InputMapper**: 在 Windows 中的位置是 `%bluestack-data-dir%BlueStacks\UserData\InputMapper`
- **Pictures**: 在 Windows 中的位置是当前用户个人主目录里的图片目录，对于 Windows 7，位于 `C:\Users\<your-user-name>\Pictures`
- **PublicPictures**: 在 Windows 中的位置是所有用户的公共文档目录， 对于 Windows 7，位于`C:\Users\Public\Pictures`
- **Documents**: 在 Windows 中的位置是当前用户个人主目录里的文档目录，对于 Windows 7，位于 `C:\Users\<your-user-name>\Documents`
- **PublicDocuments**: 在 Windows 中的位置是所有用户的公共文档目录，对于 Windows 7，位于 `C:\Users\Public\Documents`
此外，U盘之类的插到 Windows 之后，Bluestacks 会自动把U盘驱动器加到共享目录，比如U盘盘符是F，那么在安卓里面，其路径是 `/sdcard/windows/Dynamic-Volume-F`

PS. ES-Explorer 图标界面丑到爆啊。。。

### 键盘快捷键
Bluestacks 官方只介绍了一个快捷键：Ctrl+Alt+M 打开针对当前安卓应用的键盘映射工具，详细解说在[这里](http://support.bluestacks.com/hc/en-us/articles/204194239-How-to-map-keys-on-any-app-on-the-BlueStacks-App-player-)

其他我摸索出来的快捷键有：
- **Esc**: 相当于安卓的 back 软键
- **Esc + Esc**: 相当于安卓的 home 软键
- **Ctrl + ↑、↓、→、←**: 分别相当于单指向四个方向滑动屏幕，但是位置似乎是固定的，大约屏幕的上1/4处

没有摸索出相当于安卓那两个层叠方框按键的对应键盘快捷键，不爽，这样就不能关掉虚拟键那栏，本来屏幕纵向空间就捉急，该死的 16:9。

### 输入中文
装个中文输入法就OK了。
