---
title: 渐变环形进度条
date: 2016-10-23 14:21:45
tags:
---

大致效果是这样：
<!--more-->
![](http://7xt1bu.com1.z0.glb.clouddn.com/35.png)

![](http://7xt1bu.com1.z0.glb.clouddn.com/34.png)


基本原理就是遮盖了，其实图层中的一层的一半是 从黄色到蓝色的渐变  另一半是从黄色到红色的渐变
但是给一个环形的path就出现了图中效果
渐变的动画利用了 layer的 strokeEnd 属性 从 0 到 1


	#define UIColorFromRGB(rgbValue) [UIColor colorWithRed:((float)((rgbValue & 0xFF0000) >> 16))/255.0 green:((float)((rgbValue & 0xFF00) >> 8))/255.0 blue:((float)(rgbValue & 0xFF))/255.0 alpha:1.0]

	#define degreesToRadians(x) (M_PI*(x)/180.0) //把角度转换成PI的方式
	#define PROGREESS_WIDTH 80 //圆直径
	#define PROGRESS_LINE_WIDTH 4 //弧线的宽度

	#import "RRCircleProgressView.h"

	@interface RRCircleProgressView()

	@property(strong,nonatomic)UIBezierPath *path;
	@property(strong,nonatomic)CAShapeLayer *bgLayer;

	@end

	@implementation RRCircleProgressView

	- (instancetype)initWithFrame:(CGRect)frame
	{
	    self = [super initWithFrame:frame];
	    if (self) {
	        
	        
	        _path = [UIBezierPath bezierPathWithArcCenter:CGPointMake(self.bounds.size.width / 2, self.bounds.size.height / 2) radius:self.bounds.size.width / 2.5 startAngle:degreesToRadians(-225) endAngle:degreesToRadians(45)  clockwise:YES];
	    
	        
	        _bgLayer = [CAShapeLayer layer];
	        _bgLayer.frame = self.bounds;
	        _bgLayer.fillColor = [UIColor clearColor].CGColor;
	        _bgLayer.lineWidth = 16.f;
	        _bgLayer.strokeColor = [UIColor colorWithRed:0.09 green:0.10 blue:0.12 alpha:1.00].CGColor;
	        _bgLayer.strokeStart = 0.f;
	        _bgLayer.strokeEnd = 1.f;
	        _bgLayer.path = _path.CGPath;
	        [self.layer addSublayer:_bgLayer];
	        
	        _progressLayer = [CAShapeLayer layer];
	        _progressLayer.frame = self.bounds;
	        _progressLayer.fillColor = [UIColor clearColor].CGColor;
	        _progressLayer.lineWidth = 16.f;
	        _progressLayer.lineCap = kCALineCapRound;
	        _progressLayer.strokeColor = [UIColor redColor].CGColor;
	        _progressLayer.strokeStart = 0.f;
	        _progressLayer.strokeEnd = 0.f;
	        _progressLayer.path = _path.CGPath;

	        
	        CALayer *gradientLayer = [CALayer layer];
	        CAGradientLayer *gradientLayer1 =  [CAGradientLayer layer];
	        gradientLayer1.frame = CGRectMake(0, 0, self.width/2, self.height);
	        [gradientLayer1 setColors:[NSArray arrayWithObjects:
	                                   (id)[UIColorFromRGB(0xfde802) CGColor],
	                                   (id)[[UIColor colorWithRed:0.00 green:0.60 blue:1.00 alpha:1.00] CGColor],
	                                   nil]];
	        [gradientLayer1 setLocations:@[@0.1,@0.8,@1 ]];
	        [gradientLayer1 setStartPoint:CGPointMake(0.5, 0)];
	        [gradientLayer1 setEndPoint:CGPointMake(0.5, 1)];
	        [gradientLayer addSublayer:gradientLayer1];
	        
	        CAGradientLayer *gradientLayer2 =  [CAGradientLayer layer];
	        [gradientLayer2 setLocations:@[@0.1,@0.8,@1]];
	        gradientLayer2.frame = CGRectMake(self.width/2, 0, self.width/2, self.height);
	        [gradientLayer2 setColors:[NSArray arrayWithObjects:
	                                   (id)[UIColorFromRGB(0xfde802) CGColor],
	                                   (id)[[UIColor redColor] CGColor], nil]];
	        [gradientLayer2 setStartPoint:CGPointMake(0.5, 0)];
	        [gradientLayer2 setEndPoint:CGPointMake(0.5, 1)];
	        [gradientLayer addSublayer:gradientLayer2];
	        
	        [gradientLayer setMask:_progressLayer]; //用progressLayer来截取渐变层
	        [self.layer addSublayer:gradientLayer];
	        
	    }
	    return self;
	}


	- (void)startAnimation:(float)time{
	    _progressLayer.strokeEnd = 1;
	    CABasicAnimation *pathAnimation = [CABasicAnimation animationWithKeyPath:@"strokeEnd"];
	    pathAnimation.duration = time;
	    pathAnimation.fromValue = @(0);
	    pathAnimation.toValue = @(1);
	    pathAnimation.removedOnCompletion = NO;
	    [self.progressLayer addAnimation:pathAnimation forKey:nil];
	    
	}
    @end

参考和更详细的原理点 [这里](https://www.ganlvji.com/gradient_circle_progress/)


	
