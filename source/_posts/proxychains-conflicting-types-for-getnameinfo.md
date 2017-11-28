title: "libproxychains.c: error: conflicting types for 'getnameinfo'"
date: 2016-05-01 18:00:44
categories:
  - 技术
  - 软件
tags:
  - proxychain
---

在 Debian 6 (squeeze) 上编译 [proxychains](http://proxychains.sourceforge.net/) 的时候遇到编译错误：

```
libproxychains.c:291: error: conflicting types for 'getnameinfo'
/usr/include/netdb.h:677: note: previous declaration of 'getnameinfo' was here
```

打开 libproxychains.c 的第291行，看到 getnameinfo 函数的签名为：
```C
int getnameinfo (const struct sockaddr * sa,
            socklen_t salen, char * host,
            socklen_t hostlen, char * serv,
            socklen_t servlen, unsigned int flags)
```

再对比 /usr/include/netdb.h 第677行的定义：
```C
extern int getnameinfo (__const struct sockaddr *__restrict __sa,
            socklen_t __salen, char *__restrict __host,
            socklen_t __hostlen, char *__restrict __serv,
            socklen_t __servlen, int __flags);
```

很明显，签名不兼容。我当然不能修改系统头文件的函数声明，所以把 libproxychains.c 里面 getnameinfo 函数原型的最后一个参数，由`unsigned int flags`修改为与系统原型一致的`int flags`，然后再次编译，问题解决。
