---
title: objc_setAssociatedObject
date: 2016-06-19 17:36:26
tags: runtime
---

## objc_setAssociatedObject 是什么

可以动态的给一个对象增加成员变量和成员方法。
<!--more-->
Sets an associated value for a given object using a given key and association policy.

OBJC_EXPORT void objc_setAssociatedObject(id object, const void *key, id value, objc_AssociationPolicy policy)
    __OSX_AVAILABLE_STARTING(__MAC_10_6, __IPHONE_3_1);

Returns the value associated with a given object for a given key.
OBJC_EXPORT id objc_getAssociatedObject(id object, const void *key)
    __OSX_AVAILABLE_STARTING(__MAC_10_6, __IPHONE_3_1);


## 使用方法
导入头文件
	#import <objc/runtime.h>头文件

objc_setAssociatedObject需要四个参数：源对象，关键字，关联的对象和一个关联策略。
    
1 源对象alert
2 关键字 唯一静态变量key associatedkey
3 关联的对象 sender
4 关键策略  OBJC_ASSOCIATION_ASSIGN

## 例子1



给一个对象新增成员变量

BHAPI+PXExtension.h

	@interface BINGO_FITNESS_COURSE (PXExtension)

	@property (nonatomic, copy) NSString *title;
	@property (nonatomic, copy) NSString *lockImage;

	@end

BHAPI+PXExtension.m
	
	@implementation BINGO_FITNESS_COURSE (PXExtension)

	- (NSString *)title
	{
	    return objc_getAssociatedObject(self, _cmd);
	}

	- (void)setTitle:(NSString *)title
	{
	    objc_setAssociatedObject(self, @selector(title), title, OBJC_ASSOCIATION_COPY);
	}

	- (NSString *)lockImage
	{
	    return objc_getAssociatedObject(self, _cmd);
	}

	- (void)setLockImage:(NSString *)lockImage
	{
	    objc_setAssociatedObject(self, @selector(lockImage), lockImage, OBJC_ASSOCIATION_COPY);
	}

	@end

_cmd在Objective-C的方法中表示当前方法的selector(函数指针)

上面例子中 _cmd 也可以换成一个唯一的key值如 `&titleKey`  `lockImageKey`

## 例子2

给一个对象新增方法


UIAlertView+Block.h

	#import <UIKit/UIKit.h>
	typedef void (^successBlock)(NSInteger buttonIndex);

	@interface UIAlertView (Block)<UIAlertViewDelegate>

	- (void)showWithBlock:(successBlock)block;

	@end


UIAlertView+Block.m

	#import "UIAlertView+Block.h"
	#import <objc/runtime.h>

	static const char alertKey;

	@implementation UIAlertView (Block)

	- (void)showWithBlock:(successBlock)block
	{
	    if (block)
	    {
	        objc_setAssociatedObject(self, &alertKey, block, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
	        self.delegate = self;
	    }

	    [self show];
	}

	- (void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex
	{
	    successBlock block = objc_getAssociatedObject(self, &alertKey);

	    block(buttonIndex);
	}

	@end