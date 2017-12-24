---
title: Xcode8和iOS10升级问题
date: 2016-10-02 09:10:50
tags: [Xcode8,iOS10]
---

### Xcode8和iOS10升级后碰到的一些问题：
<!--more-->
1.**注释功能的解决 ，Xcode8快捷键注释的功能失效了**
  
	打开终端，命令运行： sudo /usr/libexec/xpccachectl 重启电脑

2.**如果使用了 xib和storyboard ，会出现不少警告,需要重新适配,字体所占用宽度变大**

3.**屏蔽日志  会有很多不必要的日志打印出来**

	Edit Scheme-> Run -> Arguments, 在Environment Variables里边添加 OS_ACTIVITY_MODE ＝ disable

4.**打包出现下面的错误提示，首先保证你的证书正常，然后再 targets->General->Signing里 把 Automatically manage signing 勾选去掉后再打开。 还有问题的话 请参考**

[http://stackoverflow.com/questions/37806538/code-signing-is-required-for-product-type-application-in-sdk-ios-10-0-stic](http://stackoverflow.com/questions/37806538/code-signing-is-required-for-product-type-application-in-sdk-ios-10-0-stic)

![](http://7xt1bu.com1.z0.glb.clouddn.com/31.png)

5.**权限声明，应用提交商店或真机测试的时候，记得要在Info.plist中加入所有app内使用到的权限说明，否则应用会使用崩溃。并且现在苹果在你提交应用时会自动检测你声明了这些权限没有，如果没有做会发邮件提醒你。**

![](http://7xt1bu.com1.z0.glb.clouddn.com/32.png)

6.**iOS10通知，兼容iOS10的话 请使用iOS10 的通知SDK，并且做好版本判断.**

[iOS10通知适配](http://seandeng.com/2016/09/23/iOS10Notifications/)
