---
title: Network Link Conditioner
date: 2016-05-07 00:34:06
tags: Network Link Conditioner
---

当要优化一个app在各种网络情况中的体验时，要模拟出 超时、丢包和网络中断后立即恢复的各种情况 ，这时候需要一个软件来协助你。

在Xcode中有个工具叫Network Link Conditioner（网络连接调节器），可以模拟出各种条件下的网络连接和带宽，
对iOS开发者来说这可以很方便地测试app在不同网络环境中表现。

<!--more-->
一个优化的很好的app在比较极端的网络情况下也因该保持一定水准的用户体验。

打开Xcode →[Xcode]→[Open Developer Tool]→[More Developer Tools…] 菜单项，打开下载页面。
![](http://7xt1bu.com2.z0.glb.clouddn.com/12.png)

下载页面打开以后就是这样：
在这当中有一个叫 Hardware IO Tools for Xcode 7.3 的 ， 点击下载
![](http://7xt1bu.com2.z0.glb.clouddn.com/7.png)

安装完成后就可以看到如下图的窗口，点击 Network Link Conditioner.prefPane

![](http://7xt1bu.com2.z0.glb.clouddn.com/8.png)

这个就是这款软件的界面了，非常简洁易用，

![](http://7xt1bu.com2.z0.glb.clouddn.com/9.png)

![](http://7xt1bu.com2.z0.glb.clouddn.com/10.png)

可以随意在菜单中切换 各种网络情况 如果还不能满足的话就选 customer 自定义一个
记得把开关选在on的位置 

软件会位于 “系统偏好设置” 中，打开后也会在右上角多出一个图标，方便随时disable。

然后就可以打开你的Xcode模拟器 开始优化了  

![](http://7xt1bu.com2.z0.glb.clouddn.com/11.png)
