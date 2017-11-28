title: "Currently 'rvm docs ...' does not work with non-rvm rubies."
id: 17
categories:
  - 技术
  - 软件
date: 2013-02-20 11:08:00
tags:
  - Mac OS
  - ruby
  - rvm docs
---

在Mac OS X Mountain Lion下，使用多用户模式安装rvm，然后安装了 ruby 1.9.3 之后，发现没有文档，于是试图使用以下命令生成ri的文档：
```
sudo rvm docs generate-ri
```

结果发生错误：
```
Currently 'rvm docs ...' does not work with non-rvm rubies.
```

显然sudo的rvm/ruby环境是正确的，否则sudo gem install都会有问题。不过还是检查一下：
```
$ sudo rvm ls
Password:

rvm rubies

 * ruby-1.9.3-p385 [ x86_64 ]

# => - current
# =* - current && default
#  * - default

$ sudo which ruby
/usr/local/rvm/rubies/ruby-1.9.3-p385/bin/ruby
```

貌似都没有问题。最后使用sudo -E 成功了。sudo -E 保持当前环境，但是不知道到底是哪个环境变量对此造成了影响。
```
$ sudo -E rvm docs generate-ri
Generating ri documentation, be aware that this could take a *long* time, and depends heavily on your system resources...
( Errors will be logged to /usr/local/rvm/log/ruby-1.9.3-p385/docs.log )
```
