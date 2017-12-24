---
title: Xcode Debug 选项
date: 2016-09-03 07:22:23
tags:
---


![](http://7xt1bu.com1.z0.glb.clouddn.com/29.png)

下面是一些常见的调试选项的含义

<!--more-->

### Color Blended Layers
Instruments可以在物理机上显示出被混合的图层Blended Layer(用红色标注)，
Blended Layer是因为这些Layer是透明的(Transparent)，
系统在渲染这些view时需要将该view和下层view混合(Blend)后才能计算出该像素点的实际颜色。
解决办法：检查红色区域view的opaque属性，记得设置成YES；检查backgroundColor属性是不是[UIColor clearColor]

### Color Copied Images
这个选项主要检查我们有无使用不正确图片格式,若是GPU不支持的色彩格式的图片则会标记为青色,
则只能由CPU来进行处理。我们不希望在滚动视图的时候,CPU实时来进行处理,因为有可能会阻塞主线程。
解决办法：检查图片格式，推荐使用png。

### Color Misaligned Images
这个选项检查了图片是否被放缩,像素是否对齐。
被放缩的图片会被标记为黄色,像素不对齐则会标注为紫色。    
如果不对齐此时系统需要对相邻的像素点做anti-aliasing反锯齿计算，会增加图形负担
通常这种问题出在对某些View的Frame重新计算和设置时产生的。
解决办法：参考 基本优化准则的第7点 

### Color Offscreen-Rendered
这个选项将需要offscreen渲染的的layer标记为黄色。
离屏渲染意思是iOS要显示一个视图时，需要先在后台用CPU计算出视图的Bitmap，
再交给GPU做Onscreen-Rendering显示在屏幕上，因为显示一个视图需要两次计算，
所以这种Offscreen-Rendering会导致app的图形性能下降。
大部分Offscreen-Rendering都是和视图Layer的Shadow和Mask相关。
下列情况会导致视图的Offscreen-Rendering：
- 使用Core Graphics (CG开头的类)。
- 使用drawRect()方法，即使为空。
- 将CALayer的属性shouldRasterize设置为YES。
- 使用了CALayer的setMasksToBounds(masks)和setShadow*(shadow)方法。
- 在屏幕上直接显示文字，包括Core Text。
- 设置UIViewGroupOpacity。
解决办法：只能减少各种 layer 的特殊效果了。

比如为了方便一般的Button都用 layer 的方式做圆角，这就是一种 Offscreen-Rendered