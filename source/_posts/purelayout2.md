---
title: Purelayout小结2
date: 2016-05-15 01:06:05
tags: Purelayout
---

之前小结了Purelayout 的 基本使用， 今天来总结下 等宽约束 和 动态修改约束。

<!--more-->
### 不同屏幕宽度下 等宽的两个按钮：

![](http://7xt1bu.com1.z0.glb.clouddn.com/13-2.png)


``` objc
//1.先固定重置按钮和顶部的距离40
[_resettingButton autoPinEdgeToSuperviewEdge:ALEdgeTop withInset:40];
//2.再固定重置按钮和左边的距离38
[_resettingButton autoPinEdgeToSuperviewEdge:ALEdgeLeft withInset:38.0];
//3.设定高度44
[_resettingButton autoSetDimension:ALDimensionHeight toSize:44];

//1.先固定历史按钮和顶部的距离40
[_historyButton autoPinEdgeToSuperviewEdge:ALEdgeTop withInset:40];
//2.加上和重置按钮的左边距离40
[_historyButton autoPinEdge:ALEdgeLeft toEdge:ALEdgeRight ofView:_resettingButton withOffset:40];
//3.再固定历史按钮和右边的距离38
[_historyButton autoPinEdgeToSuperviewEdge:ALEdgeRight withInset:38.0];
//4.设定高度44
[_historyButton autoSetDimension:ALDimensionHeight toSize:44];


//最后一步：设定两个按钮等宽，这样随着屏幕变化 两个按钮会有不同的宽度并且等宽。
[_historyButton autoMatchDimension:ALDimensionWidth toDimension:ALDimensionWidth ofView:_resettingButton];
```

### 动态修改约束：

![](http://7xt1bu.com2.z0.glb.clouddn.com/14.png)

比如上图中有时候会出现一个 “今天” 的图标，  hidden属性没有用， 可以用动态修改约束来解决

``` objc

首先在.h文件中创建这个要变化的约束
@property(nonatomic,strong) NSLayoutConstraint *doneDateLabelConstraint;

//课程序号标题的约束
[_courseIndexTitle autoPinEdgeToSuperviewEdge:ALEdgeTop withInset:20.0];
[_courseIndexTitle autoPinEdgeToSuperviewEdge:ALEdgeLeft withInset:15.0];

//今天按钮的约束：
[_todayIconImageView autoPinEdge:ALEdgeLeft toEdge:ALEdgeRight ofView:_courseIndexTitle withOffset:5];
//处于同一水平线的约束
[_todayIconImageView autoAlignAxis:ALAxisHorizontal toSameAxisOfView:_courseIndexTitle];
[_todayIconImageView autoSetDimension:ALDimensionWidth toSize:29];
[_todayIconImageView autoSetDimension:ALDimensionHeight toSize:13];

//完成时间的约束，只不过这个约束加完后要赋值给 doneDateLabelConstraint
_doneDateLabelConstraint = [_courseDoneDateLabel autoPinEdge:ALEdgeLeft toEdge:ALEdgeRight ofView:_courseIndexTitle withOffset:5];
//处于同一水平线的约束
[_courseDoneDateLabel autoAlignAxis:ALAxisHorizontal toSameAxisOfView:_courseIndexTitle];
```

然后用`_doneDateLabelConstraint.constant = xxx` 的方式就可以动态的改约束值了。
在 ‘今天’ 图标隐藏的时候的时候缩短 '完成时间' 与 '课程序号' 之间的距离约束。

``` objc
-(void)showTodayIcon:(BOOL)flag{
    if (flag) {
        _doneDateLabelConstraint.constant = 40.0;
        _todayIconImageView.hidden = NO;
    }else{
        _doneDateLabelConstraint.constant = 5.0;
        _todayIconImageView.hidden = YES;
    }
}
```


