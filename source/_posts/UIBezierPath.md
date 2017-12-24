---
title: UIBezierPath 画虚线圆弧
date: 2016-07-16 22:14:01
tags:
---

UIBezierPath 可以画直线 矩形 圆形 弧形
在做的项目里有个需求是画虚线并且带弧形的需求
<!--more-->
可以使用下面这个方法：

![](http://7xt1bu.com1.z0.glb.clouddn.com/24.jpeg)

	- (void)addQuadCurveToPoint:(CGPoint)endPoint 
	               controlPoint:(CGPoint)controlPoint

大致的思路就是在除了确定线的初始位置和结束位置之外，再加入一个控制点来控制弧度。

也可以用下面这个方法来做二次曲线：

![](http://7xt1bu.com1.z0.glb.clouddn.com/25.jpeg)

	- (void)addCurveToPoint:(CGPoint)endPoint 
	          controlPoint1:(CGPoint)controlPoint1 
	          controlPoint2:(CGPoint)controlPoint2


虚线较浅，在几个表情下面。

![](http://7xt1bu.com1.z0.glb.clouddn.com/23.jpg)

    CAShapeLayer *shapeLayer = [CAShapeLayer layer];
    shapeLayer.frame = self.bounds;
    
    UIBezierPath* path = [UIBezierPath bezierPath];
    path.lineCapStyle = kCGLineCapRound;
    path.lineJoinStyle = kCGLineCapRound; 
    [path moveToPoint:CGPointMake(_icon1.centerX, _icon1.bottom)];
    [path addLineToPoint:CGPointMake(_icon1.centerX, _icon1.bottom + 30)];
    [path addQuadCurveToPoint:CGPointMake(_icon1.centerX+10, _icon1.bottom + 40) controlPoint:CGPointMake(_icon1.centerX,_icon1.bottom + 40)];
    [path addLineToPoint:CGPointMake(_icon5.centerX-10, _icon5.bottom + 40)];
    [path addQuadCurveToPoint:CGPointMake(_icon5.centerX, _icon5.bottom + 30) controlPoint:CGPointMake(_icon5.centerX,_icon5.bottom + 40)];
    [path addLineToPoint:CGPointMake(_icon5.centerX, _icon5.bottom)];
    
    shapeLayer.path = path.CGPath;
    shapeLayer.fillColor = [UIColor clearColor].CGColor;
    shapeLayer.lineWidth = 1.0f;
    shapeLayer.strokeColor = [UIColor colorWithRed:233.0/255.0 green:233.0/255.0 blue:233.0/255.0 alpha:1.0].CGColor;
    [shapeLayer setLineDashPattern:[NSArray arrayWithObjects:[NSNumber numberWithInt:1], [NSNumber numberWithInt:3], nil]];
    [self.layer addSublayer:shapeLayer];

画直线的地方依然使用`addLineToPoint`, 到了要画圆弧的地方算一下位置，再确定下控制点的位置，调用`addQuadCurveToPoint`去画出圆弧。
上图中点A和点B就是圆弧的初始和结束点，点C就是控制点。

如果要画虚线就调用`[shapeLayer setLineDashPattern:xxx];` , 其中第一个参数是线的宽度，第二个是间距。

当布局使用autolayout的时候 可以先调用`[self layoutIfNeeded];`来立即布局子视图，这样就可以用子视图的frame了，否则frame都是0

 	
