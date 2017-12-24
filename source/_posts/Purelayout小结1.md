---
title: Purelayout小结1
date: 2016-04-16 23:51:14
tags: Purelayout
---

我们通常用xib文件来布局，但碰到动态的视图总是使用代码方式的AutoLayout。 很多公司还使用了 比较重的框架 比如Masonry. 
现在在做的项目中用的是一种比较轻量级别的布局类库，举一个布局的例子

上边的是view1 下边的是view2

<!--more-->

![](http://7xt1bu.com2.z0.glb.clouddn.com/5.png)

``` objc
- (void)viewDidLoad {
  [super viewDidLoad];

  //创建两个View
  UIView *view1 = [self createView];
  UIView *view2 = [self createView];

  //addSubview
  [self.view addSubview:view1];
  [self.view addSubview:view2];

  //设置view1高度为70
  [view1 autoSetDimension:ALDimensionHeight toSize:70.0];

  //view1和view2都都距离父view边距为20
  ALEdgeInsets defInsets = ALEdgeInsetsMake(20.0, 20.0, 20.0, 20.0);
  //view1 底部边距无效
  [view1 autoPinEdgesToSuperviewEdgesWithInsets:defInsets excludingEdge:ALEdgeBottom];
  //view2 顶部边距无效
  [view2 autoPinEdgesToSuperviewEdgesWithInsets:defInsets excludingEdge:ALEdgeTop];

  //两个view之间距离也是20
  [view2 autoPinEdge:ALEdgeTop toEdge:ALEdgeBottom ofView:view1 withOffset:defInsets.bottom];



	// Do any additional setup after loading the view, typically from a nib.
}

- (UIView*)createView {
  //有Autolayout不需要设置frame
  UIView *view = [UIView new];
  view.backgroundColor = [UIColor blueColor];
  //不允许AutoresizingMask转换成Autolayout, PureLayout内部也会帮你设置的。
  view.translatesAutoresizingMaskIntoConstraints = NO;

  return view;
}
```

![](http://7xt1bu.com2.z0.glb.clouddn.com/4.png)

``` objc
  //蓝色的view置于父视图的中央
  [self.blueView autoCenterInSuperview];  
  //蓝色view的大小是宽50高50
  [self.blueView autoSetDimensionsToSize:CGSizeMake(50.0, 50.0)];   

  //红色view的顶部是蓝色view的底部
  [View autoPinEdge:ALEdgeTop toEdge:ALEdgeBottom ofView:self.blueView]; 
  //红色view的左边是蓝色view的右边
  [self.redView autoPinEdge:ALEdgeLeft toEdge:ALEdgeRight ofView:self.blueView]; 
  //红色view和蓝色view同宽
  [self.redView autoMatchDimension:ALDimensionWidth toDimension:ALDimensionWidth ofView:self.blueView];
  //红色view的高度是40  
  [self.redView autoSetDimension:ALDimensionHeight toSize:40.0];

  //黄色的顶部 距离红色 底部距离为10
  [self.yellowView autoPinEdge:ALEdgeTop toEdge:ALEdgeBottom ofView:self.redView withOffset:10.0];

  // 黄色 高度 25
  [self.yellowView autoSetDimension:ALDimensionHeight toSize:25.0];

  // 黄色 与父亲左边距20
  [self.yellowView autoPinEdgeToSuperviewEdge:ALEdgeLeft withInset:20.0];

  //黄色 与父视图右边距20
  [self.yellowView autoPinEdgeToSuperviewEdge:ALEdgeRight withInset:20.0]; 

  //绿色顶部距黄色底部距离20 
  [self.greenView autoPinEdge:ALEdgeTop toEdge:ALEdgeBottom ofView:self.yellowView withOffset:10.0];

  //绿色位于父视图的水平中心
  [self.greenView autoAlignAxisToSuperviewAxis:ALAxisVertical];
  //绿色 的高度是 黄色的 两倍
  [self.greenView autoMatchDimension:ALDimensionHeight toDimension:ALDimensionHeight ofView:self.yellowView withMultiplier:2.0];

  //绿色 的宽度是 150
  [self.greenView autoSetDimension:ALDimensionWidth toSize:150.0];
```
简单好用，不过要注意的一些点： 
1.purelayout提供的方法有些是只支持iOS8及以上的，如果iOS7及以下的调用了是会奔溃的。 
2.在view父容器为nil的时候，执行purelayout的方法会崩溃。所以很多时候要先把view加入到父视图中后，再去使用prelayout方法，这样父视图不会为nil。
