---
title: 自定义UIButton
date: 2016-07-24 11:41:52
tags:
---

需要做如下图样式的按钮，虽然用UIView可以方便的布局出效果，但缺少点击的效果，所以还是要在UIButton上做定制。
<!--more-->

普通样式:
![](http://7xt1bu.com1.z0.glb.clouddn.com/26.png)  
点击样式:
![](http://7xt1bu.com1.z0.glb.clouddn.com/27.png)


PXBoxMeButton.h
	
	@interface PXBoxMeButton : UIButton
    //下面几个属性是雷达图专属
	@property(nonatomic,strong) UILabel *subTitleLabel;
	@property(nonatomic,strong) UIImageView *rightBottomImageView;
	@end

PXBoxMeButton.m

	#import "PXBoxMeButton.h"

	@implementation PXBoxMeButton

	- (id)initWithFrame:(CGRect)frame
	{
	    self = [super initWithFrame:frame];
	    if (self){
	        _subTitleLabel = [[UILabel alloc]init];
	        _subTitleLabel.text = @"(每周日更新)";
	        _subTitleLabel.font = [UIFont systemFontOfSize:9];
	        _subTitleLabel.textColor = [UIColor lightGrayColor];
	        _subTitleLabel.textAlignment = NSTextAlignmentCenter;
	        [self addSubview:_subTitleLabel];
	        _subTitleLabel.hidden = YES;
	        
	        
	        _rightBottomImageView = [[UIImageView alloc]init];
	        [self addSubview:_rightBottomImageView];
	        _rightBottomImageView.hidden = YES;
	    }
	    return self;
	}

	- (void)layoutSubviews
	{
	    [super layoutSubviews];
	    
	    //center Image
	    CGPoint center = self.imageView.center;
	    center.x              = self.frame.size.width / 2;
	    center.y              = self.frame.size.width / 2.5;
	    self.imageView.center = center;
	    
	    //center text
	    CGRect newFrame = self.titleLabel.frame;
	    newFrame.origin.x             = 0;
	    newFrame.origin.y             = self.imageView.bottom + 5;
	    newFrame.size.width           = self.frame.size.width;
	    self.titleLabel.frame         = newFrame;
	    self.titleLabel.textAlignment = NSTextAlignmentCenter;
	    
	    //center subtext
	    [_subTitleLabel sizeToFit];
	    _subTitleLabel.top =  self.titleLabel.bottom+5;
	    _subTitleLabel.width = self.frame.size.width;
	    
	    _rightBottomImageView.x = self.frame.size.width *7/10;
	    _rightBottomImageView.y = self.frame.size.width *7/10;
	    _rightBottomImageView.width = self.frame.size.width/10*3;
	    _rightBottomImageView.height = self.frame.size.width/10*3;
	}

	@end

在 layoutSubviews 方法中重新布局默认的 imageView 和 titleLabel，本来是左右放置，现在改为上下。 也加上自定义的 _subTitleLabel（副标题） 和 _rightBottomImageView （角标）。在接口中的属性可以方便的控制角标和副标题，保持按钮点击的样式。

