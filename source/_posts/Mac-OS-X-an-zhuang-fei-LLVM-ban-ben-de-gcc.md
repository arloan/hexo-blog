title: Mac OS X 安装非 LLVM 版本的 gcc
id: 18
categories:
  - 技术
  - 软件
date: 2013-02-17 14:05:00
tags:
  - gcc
  - homebrew
  - llvm
  - Mac OS
  - XCode
---

XCode 4.3 的gcc已经是llvm版本的（之前的版本已经不记得了，似乎至少从4.2开始就只有llvm了？），而通过rvm安装ruby 1.9.3时，要求非llvm的gcc：apple-gcc42。
```
$ sudo rvm install ruby-1.9.3-head
...

The provided compiler '/usr/bin/gcc' is LLVM based, it is not yet fully supported by ruby and gems, please read `rvm requirements`.
```

然而默认情况下，homebrew无法安装该版本的gcc：
```
$ brew install apple-gcc42 
Error: No available formula for apple-gcc42
```

搜索该formula，得到其位置：
```
$ brew search gcc42

homebrew/dupes/apple-gcc42
```

然后执行命令：
```
$ brew tap homebrew/dupes
```

成功之后就可以安装apple-gcc42了。
