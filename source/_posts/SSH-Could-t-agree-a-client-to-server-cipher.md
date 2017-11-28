title: "SSH: Could't agree a client-to-server cipher"
date: 2015-12-17 19:32:40
categories:
  - 技术
  - 软件
tags:
  - SSH
---

SSH 客户端我一直都使用一个单文件程序：[PieTTY](http://ntu.csie.org/~piaip/pietty/)，颇为好用。不过这个程序好些年都没有更新过了，直到最近发现某些 Server 连不上去了，比如越狱苹果上的 OpenSSH Server。连接时出现一个错误：

![SSH Connection Error](/comm-res/images/SSH-cipher.png)

Couldn't agree a client-to-server cipher (available: aes128-ctr,aes192-ctr,aes256-ctr,chacha20-poly1305@openssh.com)

放狗搜了一下，应该是最近认为加密算法采用 CBC [block cipher mode of operation](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation) 已经不够安全，所以比较新的 SSH Server 默认已经不再使用 CBC 模式。而古老的 PieTTY 则不支持 CTR 模式，因此在与服务器协商加密协议时失败了。错误文本里面的 `available` 后面的算法是当前服务器所支持的算法。

好吧，我也不是特别在意这么潜在的安全问题，考虑了一下还是放弃了更换 SSH 客户端的想法，采用了给服务器加上不那么安全的算法的解决办法。。。

当然前提是找个能登入目标服务器的 SSH 客户端啦，我用的是 Cygwin 自带的命令行。登入服务器之后，修改 /etc/ssh/sshd_config 文件，注意是 `sshd_config` 而不是 `ssh_config`，后者是 Client 设置。寻找有无名为 `Ciphers` 的配置项，如果没有，就自行加上。配置项长这个样子：

```
Ciphers aes128-ctr,aes192-ctr,aes256-ctr,chacha20-poly1305@openssh.com
```
如果没有，那就手动加上一个，注意算法要跟连接报错时提供的 available 列表一致，然后在最后加上 `aes256-cbc` 就OK了，像这样：
```
Ciphers aes128-ctr,aes192-ctr,aes256-ctr,chacha20-poly1305@openssh.com,aes256-cbc
```

保存后让 sshd 重新加载配置就可以了。
![SSH 连接手机成功！](/comm-res/images/SSH-cipher-OK.png)
