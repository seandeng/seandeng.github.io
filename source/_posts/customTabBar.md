---
title: 自定义 tabbar
date: 2016-09-11 10:48:24
tags:
---

系统定义的tabbar有时无法满足需求，需要自己定义。比如中间加一个按钮之类。
<!--more-->
![](http://7xt1bu.com1.z0.glb.clouddn.com/30.png)


自定义一个tabbar,继承UITabBar。

### 在PXTabBar中我们可以在中间加一个按钮

	UIButton *plusBtn = [[UIButton alloc] init];
	[plusBtn setBackgroundImage:[UIImage imageNamed:@"ic_home_add"] forState:UIControlStateNormal];
	plusBtn.size = plusBtn.currentBackgroundImage.size;
	[plusBtn addTarget:self action:@selector(plusClick) forControlEvents:UIControlEventTouchUpInside];
	[self addSubview:plusBtn];
	self.plusBtn = plusBtn;

### 然后将原来的四个按钮重新布局

	-(void)layoutSubviews {
	    [super layoutSubviews];
	    
	    self.plusBtn.centerX = self.width * 0.5;
	    self.plusBtn.centerY = self.height * 0.5;
	    
	    //设置其他tabbarButton的位置和尺寸
	    CGFloat tabBarButtonW  = self.width / 5;
	    CGFloat tabbarButtonIndex = 0;
	    
	    for (UIView *child in self.subviews) {
	        Class class = NSClassFromString(@"UITabBarButton");
	        if ([child isKindOfClass:class]) {
	            
	            if (tabbarButtonIndex == 2) {
	                tabbarButtonIndex = 3;
	            }
	            
	            child.x = tabbarButtonIndex *tabBarButtonW;
	            child.width = tabBarButtonW;
	            
	            tabbarButtonIndex ++;
	        }
	    }
	}


### KVC 

UITabBarController 中有一个属性叫 UITabBar *tabBar， UITabBar 实际是一个 UIView

	@property(nonatomic,readonly) UITabBar *tabBar NS_AVAILABLE_IOS(3_0); // Provided for -[UIActionSheet showFromTabBar:]. Attempting to modify the contents of the tab bar directly will throw an exception.

但是这个属性是只读的，想要更换这个tabbar就需要使用 KVC.

KVC就是Key-value coding，大意是允许通过一个Key来读写一个value。
最最常见就是

	[id setValue: forKey:]
	[id valueforKey:]

这两个方法允许你指定一个Key，然后通过这个Key去访问指定对象中的value。

最后在自定义的 TabBarViewController 中使用KVC来替换掉系统的 tabbar。

    PXTabBar *tab = [[PXTabBar alloc]init];
    tab.pxDelegate = self;
    [self setValue:tab forKey:@"tabBar"];





