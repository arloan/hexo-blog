title: 如何在 Mac OS X 中重置 DNS 缓存
categories:
  - 技术
  - 软件
date: 2015-09-15 20:10:45
tags:
  - Mac OS
---

苹果官方原文**[在此](https://support.apple.com/en-us/HT202516)**。

### OS X Yosemite
1. Yosemite v10.10.4
```
sudo killall -HUP mDNSResponder
```

2. Yosemite v10.10 ~ v10.10.3
```
sudo discoveryutil mdnsflushcache
```
PS. 真搞不懂苹果是啥意思……


### OS X Mavericks, Mountain Lion, Lion
```
sudo killall -HUP mDNSResponder
```

### OS X Snow Leopard
```
sudo dscacheutil -flushcache
```
