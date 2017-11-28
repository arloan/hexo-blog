title: TinyCC __fastcall bug
categories:
  - 技术
  - 软件
date: 2015-08-21 13:02:05
tags:
  - TinyCC
---

[TinyCC](http://bellard.org/tcc/) 是一个体积非常小，编译速度非常快，支持 ansi C99 的跨平台C编译器，我用来做 [Nim](http://nim-lang.org/) 这个语言的后端，不过刚开始用就发现一个BUG：其 Windows 32bit 版本使用 __stdcall 调用约定时，如果参数是64位则结果不正确。

Windows 64位版本没有问题，其他操作系统版本未测试。


### 测试环境：
Windows 7 ultimate x64, TinyCC 0.9.26

### 测试代码：

{% codeblock lang:c %}
#include <stdio.h>

void __fastcall go(double d)
{
        printf("go fastcall: %f\n", d);
}
void __cdecl go2(double d)
{
        printf("go cdecl: %f\n", d);
}
void __fastcall gof(float d)
{
        printf("go fastcall float: %f\n", d);
}
void __fastcall golong(__int64 n)
{
        printf("go fastcall int64: 0x%016I64X\n", n);
}

int main(int argc, char * argv[])
{
        double pi = 3.14;
        go(pi);
        go2(pi);
        gof((float)pi);
        golong(0x1234567812345678);
        return 0;
}
{% endcodeblock %}

### 测试结果：

- 32位TinyCC：
```
D:\bullshit>d:\devel\tcc\tcc -run noname2.c
go fastcall: 0.000000
go cdecl: 3.140000
go fastcall float: 3.140000
go fastcall int64: 0x0028FEA812345678

D:\bullshit>
```


- 64位TinyCC：
```
D:\bullshit>d:\devel\tcc64\tcc -run noname2.c
go fastcall: 3.140000
go cdecl: 3.140000
go fastcall float: 3.140000
go fastcall int64: 0x1234567812345678

D:\bullshit>
```

虽然TinyCC是开源的，不过现在已经没有人维护了，所以我也没有提交BUG。
