---
title: iOS 旋屏控制
date: 2016-05-21 20:13:49
tags:
---

在iOS5.1 和 之前的版本中， 我们通常利用 shouldAutorotateToInterfaceOrientation: 来单独控制某个UIViewController的旋屏方向支持，比如：
<!--more-->

``` objc
- (BOOL)shouldAutorotateToInterfaceOrientation:(UIInterfaceOrientation)interfaceOrientation
{
    return (interfaceOrientation == UIInterfaceOrientationPortrait);
}
```

但是在iOS6中，这个方法被废弃了，使用无效。

iOS 6及之后的版本

在 iOS 6 及之后的版本，单个界面的屏幕方向控制，要使用 UIViewController 在 iOS 6.0 中新增加的两个方法：

1.
``` objc
// 是否支持转屏
- (BOOL)shouldAutorotate
{
    return YES;
}
```

2.
``` objc
// 支持的屏幕方向，此处可直接返回 UIInterfaceOrientationMask 类型
// 也可以返回多个 UIInterfaceOrientationMask 取或运算后的值
- (NSUInteger)supportedInterfaceOrientations
{
    return UIInterfaceOrientationMaskLandscape;
}
```



iOS6之后还提供了这样一个方法，可以让你的Controller倔强第坚持某个特定的interfaceOrientation:


	- (UIInterfaceOrientation)preferredInterfaceOrientationForPresentation NS_AVAILABLE_IOS(6_0);


这里使用的是另外一套枚举量，可以去UIApplication.h中查看定义。

	controller.interfaceOrientation，获取特定controller的方向
	[[UIApplication sharedApplication] statusBarOrientation] 获取状态条相关的方向
	[[UIDevice currentDevice] orientation] 获取当前设备的方向

总结下，在现在的开发中已经是基本支持iOS7以上了，所以旋屏的方法如下


子类化UINavigationController，增加方法

	- (BOOL)shouldAutorotate
	{
	    return self.topViewController.shouldAutorotate;
	}

	- (NSUInteger)supportedInterfaceOrientations
	{
	    return self.topViewController.supportedInterfaceOrientations;
	}

并且设定其为程序入口，或指定为 self.window.rootViewController
随后添加自己的view controller，如果想禁止某个view controller的旋屏：

	-(BOOL)shouldAutorotate  
	{  
	    return NO;  
	}  
	  
	-(NSUInteger)supportedInterfaceOrientations  
	{  
	    return UIInterfaceOrientationMaskPortrait;  
	} 

薄荷的rootViewController 不是 navigationController 是PXRootViewController

@property (nonatomic, readonly) UIViewController *selectTabViewController;
@property (nonatomic, weak) UITabBarController *homeTabBarViewController;

这是一个包裹了 PXHomeTabBarViewController 的 viewController

	- (BOOL)shouldAutorotate
	{
	    return [self.homeTabBarViewController.selectedViewController shouldAutorotate];
	}

	- (UIInterfaceOrientationMask)supportedInterfaceOrientations
	{
	    return [self.homeTabBarViewController.selectedViewController supportedInterfaceOrientations];
	}


