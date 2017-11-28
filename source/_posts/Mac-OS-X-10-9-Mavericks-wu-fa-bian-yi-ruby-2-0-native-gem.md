title: 'Mac OS X 10.9 Mavericks: 无法编译ruby 2.0 native gem'
id: 7
categories:
  - 技术
  - 软件
date: 2013-10-09 23:02:00
tags:
  - Mac OS
  - Mavericks
  - ruby
---

Mac OS X 10.9 Mavericks 自带的ruby升级到了2.0。
使用gem install安装软件包，发现但凡需要编译native组件的gem都会失败，因为编译过程报告找不到ruby.h：
```
1 $ sudo gem install nokogiri

Building native extensions.  This could take a while...
ERROR:  Error installing nokogiri: ERROR: Failed to build gem native extension.
    /System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/bin/ruby extconf.rb
mkmf.rb can't find header files for ruby at /System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/lib/ruby/include/ruby.h
Gem files will remain installed in /Library/Ruby/Gems/2.0.0/gems/nokogiri-1.6.0 for inspection.
Results logged to /Library/Ruby/Gems/2.0.0/gems/nokogiri-1.6.0/ext/nokogiri/gem_make.out
```

这个问题以前通常是因为没有安装命令行编译工具，但这次不是：我已经安装了XCode 5，命令行gcc运行正常。通过[上一篇博文](/2013/10/ru-he-zai-Mac-OS-X-10-9-Mavericks-zhong-an-zhuang-Command-Line-Tools/)中的方法安装command line tools也不解决问题。
后来发现ruby.h其实已经存在，只不过其位置不符合rubygem的期望，于是做了两个符号链接解决问题：
```
2 $ sudo ln -s /System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/include/ruby-2.0.0 /System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/lib/ruby/include3 $ sudo ln -s /System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/include/ruby-2.0.0/universal-darwin13/ruby/config.h /System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/include/ruby-2.0.0/ruby/config.h
```

之后安装需要编译native部件的gem就都正常了。

