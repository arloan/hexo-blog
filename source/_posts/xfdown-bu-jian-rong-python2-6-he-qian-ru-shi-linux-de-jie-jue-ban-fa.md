title: xfdown不兼容python2.6和嵌入式linux的解决办法
id: 14
categories:
  - 技术
  - 软件
date: 2013-04-26 15:38:00
tags:
  - xfdown
  - python
  - linux
  - QQ旋风
  - 嵌入式
---

毫无疑问，[xfdown](https://github.com/kikyous/xfdown)是个好东西（不解释），不过在python2.6下面无法登录。在输入完qq号和口令之后，出现以下错误：
```
QQ：xxxxxxxx
PASSWD: 
Traceback (most recent call last):
  File "./xfdown.py", line 113, in
    xf = XF()
  File "/data/root/opensource/xfdown/xfdown_api.py", line 165, in __init__
    self.__Login(True)
  File "/data/root/opensource/xfdown/xfdown_api.py", line 368, in __Login
    self.__verifycode
  File "/data/root/opensource/xfdown/xfdown_api.py", line 134, in __preprocess
    H = self.__md5(I + verifycode[2])
  File "/data/root/opensource/xfdown/xfdown_api.py", line 147, in __md5
    return hashlib.md5(item).hexdigest().upper()
TypeError: md5() argument 1 must be string or read-only buffer, not bytearray
```

解决方法：
用编辑器打开xfdown_api.py，跳到147 行，加入以下两行：
```
147         if sys.version_info < (2,7):
148             item=str(item)
```
保存以后即可在python2.6下使用。

有些嵌入式linux下可能无法运行，例如我的开博尔K660i高清播放机的操作系统：
`Linux localhost 2.6.34-VENUS #28 PREEMPT Sat Dec 10 01:14:57 HKT 2011 mips GNU/Linux`

安装了optware的python2.6，并经过前述修改后，xfdown登录成功之后立刻报错退出：
```
File "/opt/lib/python2.6/site-packages/urwid/canvas.py", line 1239, in apply_text_layout
    text[s.offs:s.end])
  File "/opt/lib/python2.6/site-packages/urwid/util.py", line 127, in apply_target_encoding
    s = s.encode( _target_encoding )
UnicodeEncodeError: 'ascii' codec can't encode character u'\u2019' in position 58: ordinal not in range(128)
```
猜测是系统没有默认locale的原因，环境变量LANG未定义。于是export LANG=en_US.UTF-8再运行，果然可以工作了。