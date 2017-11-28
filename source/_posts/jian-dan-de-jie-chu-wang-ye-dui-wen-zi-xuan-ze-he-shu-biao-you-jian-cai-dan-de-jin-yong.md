title: 简单的解除网页对文字选择和鼠标右键菜单的禁用
id: 19
categories:
  - 技术
  - 软件
date: 2006-10-19 00:05:00
tags:
  - javascript
  - Web
  - IE
---

现在有许多网页禁用了IE下的鼠标右键菜单和文字选取功能，究其原因，可能是为了防止资料被拷贝。然而对于一个网页而言，既然都被传输到了本地，你想怎么限制都是徒劳。下面提供一个非常简单的办法解除这些限制。

1\. 解除IE右键限制：
例如这个地址就禁止了右键菜单：[http://www.cssn.net.cn/pages/search/standardList.jsp?fl=2&code=01.040.25](http://www.cssn.net.cn/pages/search/standardList.jsp?fl=2&code=01.040.25)
打开页面以后，在地址栏中输入以下内容，然后回车即可解除限制。
```
javascript:function f(){document.body.oncontextmenu=null;} f();
```

如图所示：
![截图](/comm-res/images/SNAG-0022.png)

2\. 解除选择限制。
上面这个页面同时也禁止了选择，解除限制的方法也同样很简单。打开页面之后，在地址栏输入以下内容，然后回车即可。

```
javascript:function g(){var b=document.body;b.onselect=null;b.onselectstart=null;}g();
```