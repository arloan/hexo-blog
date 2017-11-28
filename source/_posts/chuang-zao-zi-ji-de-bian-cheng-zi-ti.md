title: 创造自己的编程字体
id: 26
categories:
  - 技术
  - 软件
date: 2006-10-08 13:12:00
tags:
  - 字体
---

作为一个有品位的程序员，对使用什么字体写代码应当是挑剔的。

首先这个字体必须是等宽的，否则代码中段无法对齐，也就无法写出格式漂亮的代码来。（题外话：最早写代码时，我用过Times New Roman）其次是数字1和字母l（小写），数字0和字母O（大写），两个连续的单引号'和一个双引号"必须能区分清楚。再次呢，自然是自己觉得漂亮。最后，为了代码中的中文和英文能对齐，这个字体最好是宽度等于高度的一半，例如6x12，7x14等。

大多数VC6程序员使用Fixed System，大多数VC2003程序员使用宋体，是因为这两种字体分别是这两套IDE的缺省字体。然而，这两种字体的缺陷是很明显的：Fixed System没有粗体，并且字形太大导致一屏看不了几行代码（不过如今大屏幕高分辨率LCD的普及使这个问题显得不那么明显了），而宋体的英文字体，那是相当的丑啊！尤其是m/M的粗体，简直是无法忍受的。

于是，一些稍微有追求的程序员使用Courier New或者Andale Mono。然而不幸的是，Courier New的0O，1l无法区分，而且行距太大，而Andale Mono字形过于个性（导致某些人觉得好看，而另一些人觉得十分难看，比如我），并且没有内嵌点阵字体，导致必须用超过11pt的字体才不显得模糊，因此并非最佳选择。

在用过无数种字体之后，我终于无法忍受了，于是决定创造自己的编程字体。

为了字形清晰，我选择了点阵（光栅）字体，即.FON字体。

首先，你需要用VC6编译一份光栅字体编辑器。你可以从2001年10月份的MSDN中找到该编辑器的工程代码（搜索"FontEdit: Font Editor Sample"）。之后版本的MSDN似乎已经没有这份代码了。

然后从[这里](http://download.microsoft.com/download/platformsdk/myfont/3.1/W31/EN-US/MyFont.exe "MyFont.exe")下载微软提供的初始.FNT光栅字体包，这是个16位DOS ZIP自解压包，解开后有一个MYFONT.FNT文件。由于前面的字体编辑器（FontEdit.exe）无法创建新的.FNT，所以你必须得到这个字体包。

好了，用fontedit.exe打开MYFONT.FNT，修改字体大小（font->size）和字体名（font->header），再把每个字符按自己的审美画好，另存为自己喜欢的名字，比如hello.fnt。

打开你喜欢的任何文本编辑器，输入以下内容：

`31 FONT HELLO.FNT`

保存为hello.fnt同一目录下的文件：hello.rc

打开VC的命令行编译环境（vs2003很方便，开始->程序->Microsoft Visual Studio .NET 2003->Visual Studio .NET Tools->Visual Studio .NET 2003 Command Prompt），进入到HELLO.FNT所在目录，然后依次执行以下命令：
```
RC /fo hello.res hello.rc
LINK /DLL /NOENTRY /MACHINE:IX86 /FIXED /OUT:hello.fon hello.res
```

OK，属于你自己的字体：hello.fon就做好啦！赶紧拷贝到windows的字体目录就可以使用啦！

看看我的作品：

![我的字体](/comm-res/images/My-own-font.png)