title: xfdown在嵌入式linux上无法下载的问题
id: 12
categories:
  - 技术
  - 软件
date: 2013-04-26 16:48:00
tags:
  - xfdown
  - aria2c
  - QQ旋风
  - 高清播放机
---

在上一篇博文中解决了xfdown的运行之后，又发现其无法下载任何文件。回车下载报错：

`[ DownloadEngineFactory.cc:97] errorCode=1 Initializing EpollEventPoll failed.  Try --event-poll=select ...`

xfdown使用aria2c这个并行下载工具进行下载。按照提示，需要修改其event-poll的参数。用文本编辑器打开xfdown_api.py，定位到114行，将该行：
`cmd=['aria2c', '-c', '-s10', '-x10', '--header', 'Cookie:ptisp=edu; FTN5K=%s'%self.filecom[i], '%s'%self.filehttp[i]]`
修改为
`cmd=['aria2c', '-c', '-s10', '-x10', '--event-poll', 'select', '--header', 'Cookie:ptisp=edu; FTN5K=%s'%self.filecom[i], '%s'%self.filehttp[i]]`

保存退出，xfdown即可正常工作了。下载完一个文件之后可能会报告：
`notify-send error,you should have libnotify-bin installed.`
不过不影响。这个事libnotify-bin包没有移植到optware的原因，暂时无法解决。有兴趣的童鞋可以自行移植一把试试。
