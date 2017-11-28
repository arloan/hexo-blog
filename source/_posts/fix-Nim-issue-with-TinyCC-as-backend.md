title: 修复 Nim 使用 TinyCC 作为后端的问题
categories:
  - 技术
  - 软件
date: 2015-08-22 17:13:37
tags:
  - Nim
  - TinyCC
---

在[上一篇](/2015/08/TinyCC-fastcall-bug/)文章里说到用 32bit TinyCC 作为 Nim 编译器后端时，当函数参数为64位时会出现运行结果错误的情况，这其实是由于 Nim 的 `nimbase.h` 文件里面，没有针对 TinyCC 的 __fastcall 调用约定声明格式不同于其他常见编译器格式做出专门调整的缘故。虽然我已经在 Github 上针对这个问题提了个包含解决方案的 *[issue](https://github.com/nim-lang/Nim/issues/3237)*，不过在 Nim 官方接受这个方案之前，我们还是需要自己动手：

用你喜爱的代码编辑器打开 Nim 安装目录下的 `lib/nimbase.h` 这个文件，找到第 150~152 行：

```c
#  define N_NIMCALL(rettype, name) rettype __fastcall name
#  define N_NIMCALL_PTR(rettype, name) rettype (__fastcall *name)
#  define N_RAW_NIMCALL __fastcall
```

将这几行替换为下面的内容即可解决问题：
```c
#  ifdef __TINYC__
#    define N_NIMCALL(rettype, name) rettype __attribute((__fastcall)) name
#    define N_NIMCALL_PTR(rettype, name) rettype (__attribute((__fastcall)) *name)
#    define N_RAW_NIMCALL __attribute((__fastcall))
#  else
#    define N_NIMCALL(rettype, name) rettype __fastcall name
#    define N_NIMCALL_PTR(rettype, name) rettype (__fastcall *name)
#    define N_RAW_NIMCALL __fastcall
#  endif
```
