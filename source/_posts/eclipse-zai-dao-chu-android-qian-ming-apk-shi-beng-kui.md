title: Eclipse在导出Android签名APK时崩溃
id: 104
categories:
  - 技术
  - 软件
date: 2014-02-18 15:54:32
tags:
  - Android
  - eclipse
---

> Eclipse Juno Service Release 2
> Android SDK 4.2.2
> Mac OS X 10.8.5


右键工程->Android Tools->Export Signed Application Package ...

输入完Keystore密码之后，Eclipse崩溃。解决办法有2：

1. 取消自动编译，即去掉Eclipse的Project-&gt;Build Automatically的勾选状态；

2. 将编译输出修改为Normal，即：打开Eclipse的配置对话框（Windows: Window->Preference，Mac: Eclipse->Preference），左侧定位到Android->Build，将右侧的"Build Output"选中为Normal（图中红圈）。
![eclipse-preferences](/comm-res/images/eclipse-preferences.png)

以上方案中（1）解决了我的问题，（2）的话未经测试。
