title: 查找定位 objc block 的汇编代码
date: 2015-12-19 18:56:55
categories:
  - 技术
  - 软件
tags:
  - iOS
  - objc
  - Crack
---

在逆向分析某 iOS App 时，我发现我关注的功能是在某 Class 的单例（Singleton）初始化时实现的，而其单例实现采用了主流的 [dispatch_once](https://developer.apple.com/library/ios/documentation/Performance/Reference/GCD_libdispatch_Ref/#//apple_ref/c/func/dispatch_once) + block 的方式，类似如下代码：
```objc
+ (IMSExample *)sharedInstance {
    static IMSExample * _theOne = NULL;
    static dispatch_once_t once = 0;
    dispatch_once(&once, ^{
        _theOne = [[self alloc] init];
        // additional initialize code
    });
    return _theOne;
}
```

在我的分析目标代码中，对象的初始化工作没有放在`init`方法里面，而是放在了如上面演示的`addtional initialize code`处的`block`代码块中，这就不容易定位其相应汇编代码，给分析工作带了了一点障碍。下面是分析目标的单例实现汇编代码：

![Singletion](/comm-res/images/2015-12/disassembler-singleton.png)

`dispatch_once`的原型是：
```objc
void dispatch_once(dispatch_once_t *predicate, void (^block)(void));
```
由此可知，上图划红线处，寄存器 R1 的内容即 block 的地址。那么这个地址到底是什么内容呢？实际上是个结构体：
```
struct Block_layout { 
    void *isa; 
    int flags; 
    int reserved; 
    void (*invoke)(void *, ...); 
    struct Block_descriptor *descriptor; 
    /* Imported variables. */ 
}; 
```

该结构体的第一个成员`isa`指向的是`__NSConcreteGlobalBlock`，而第四个成员`invoke`（32位的话，偏移量为12字节，64位的话，偏移量为16字节）就是我们关心的 block 的实际功能代码。

接下来，在 IDA 中双击 R1 寄存器的值`off_46DFC8`，可以看到这个地址指向的正是上述`isa`成员：`__NSConcreteGlobalBlock`：
![Block Layout](/comm-res/images/2015-12/disassembler-block-layout.png)

`__NSConcreteGlobalBlock`的地址是`0046DFC8`，偏移12字节是`0046DFD4`，其内容就是我要找的 block 实际功能代码的入口子过程（划紫线处）：`sub_7B13C`。双击该子过程，就定位到我需要分析的代码了：

![Block Code](/comm-res/images/2015-12/disassembler-block.png)

可见这段代码只是初始化了一些函数指针。至于这些指针指向的函数做了什么事情，就不在本文的范围之内了，本文只是备忘一下定位 block 代码块的过程而已。
