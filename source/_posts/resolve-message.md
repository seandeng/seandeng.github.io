---
title: 消息动态解析
date: 2016-07-03 00:28:55
tags: runtime
---

## 消息动态解析

消息动态解析是 objc_msgSend 函数调用过程的最后一步
<!--more-->
objc_msgSend(receiver, selector);
objc_msgSend(peopleInstance, s, @"大家好!");


回顾一下前三步：
第一步：检测这个selector是不是要忽略的。
第二步：检测这个target是不是nil对象。nil对象发送任何一个消息都会被忽略掉。
第三步：
1.调用实例方法时，它会首先在自身isa指针指向的类（class）methodLists中查找该方法，如果找不到则会通过class的super_class指针找到父类的类对象结构体，然后从methodLists中查找该方法，如果仍然找不到，则继续通过super_class向上一级父类结构体中查找，直至根class；
2.当我们调用某个某个类方法时，它会首先通过自己的isa指针找到metaclass，并从其中methodLists中查找该类方法，如果找不到则会通过metaclass的super_class指针找到父类的metaclass对象结构体，然后从methodLists中查找该方法，如果仍然找不到，则继续通过super_class向上一级父类结构体中查找，直至根metaclass；
第四步：前三部都找不到就会进入动态方法解析(看下文)。




动态解析流程图

![](http://7xt1bu.com1.z0.glb.clouddn.com/17.png)

第一步：通过resolveInstanceMethod：方法决定是否动态添加方法。如果返回Yes则通过class_addMethod动态添加方法，消息得到处理，结束；如果返回No，则进入下一步；
第二步：这步会进入forwardingTargetForSelector:方法，用于指定备选对象响应这个selector，不能指定为self。如果返回某个对象则会调用对象的方法，结束。如果返回nil，则进入第三部；
第三部：这步我们要通过methodSignatureForSelector:方法签名，如果返回nil，则消息无法处理。如果返回methodSignature，则进入下一步；
第四部：这步调用forwardInvocation：方法，我们可以通过anInvocation对象做很多处理，比如修改实现方法，修改响应对象等，如果方法调用成功，则结束。如果失败，则进入doesNotRecognizeSelector方法，若我们没有实现这个方法，那么就会crash。

## 例子1 

添加sing实例方法，但是不提供方法的实现。验证当找不到方法的实现时，动态添加方法。

PeopleB.h

	@interface PeopleB : NSObject
	@property (nonatomic, copy) NSString *name;
	- (void)sing;
	@end

PeopleB.m

	#import "PeopleB.h"
	#if TARGET_IPHONE_SIMULATOR
	#import <objc/objc-runtime.h>
	#else
	#import <objc/runtime.h>
	#import <objc/message.h>
	#endif

	@implementation PeopleB

	+ (BOOL)resolveInstanceMethod:(SEL)sel
	{
	    // 我们没有给People类声明sing方法，我们这里动态添加方法
	    if ([NSStringFromSelector(sel) isEqualToString:@"sing"]) {
	        class_addMethod(self, sel, (IMP)otherSing, "v@:");
	        return YES;
	    }
	    return [super resolveInstanceMethod:sel];
	}

	void otherSing(id self, SEL cmd)
	{
	    NSLog(@"%@ 唱歌啦！",((PeopleB *)self).name);
	}
	

main.m

	PeopleB *people = [[PeopleB alloc] init];
	people.name = @"jack";
	[people sing];

运行结果

![](http://7xt1bu.com1.z0.glb.clouddn.com/18.png)

## 例子2  

不声明sing方法，将调用途中动态更换调用对象

Bird.h

	#import <Foundation/Foundation.h>

	@interface Bird : NSObject

	@property (nonatomic, copy) NSString *name;

	@end

Bird.m

	#import "Bird.h"
	#import "PeopleB.h"
	@implementation Bird

	// 第一步：我们不动态添加方法，返回NO，进入第二步；
	+ (BOOL)resolveInstanceMethod:(SEL)sel
	{
	    return NO;
	}

	// 第二部：我们不指定备选对象响应aSelector，进入第三步；
	- (id)forwardingTargetForSelector:(SEL)aSelector
	{
	    return nil;
	}

	// 第三步：返回方法选择器，然后进入第四部；
	- (NSMethodSignature *)methodSignatureForSelector:(SEL)aSelector
	{
	    if ([NSStringFromSelector(aSelector) isEqualToString:@"sing"]) {
	        return [NSMethodSignature signatureWithObjCTypes:"v@:"];
	    }
	    
	    return [super methodSignatureForSelector:aSelector];
	}

	// 第四部：这步我们修改调用对象
	- (void)forwardInvocation:(NSInvocation *)anInvocation
	{
	    // 我们改变调用对象为People
	    PeopleB *jack = [[PeopleB alloc] init];
	    jack.name = @"Tom";
	    [anInvocation invokeWithTarget:jack];
	}

	@end

main.m

	Bird *bird = [[Bird alloc] init];
	bird.name = @"小小鸟";

	((void (*)(id, SEL))objc_msgSend)((id)bird, @selector(sing));


运行结果

![](http://7xt1bu.com1.z0.glb.clouddn.com/19.png)


## 例子3

实现不提供声明，不修改调用对象，但是将sing方法修改为dance方法

PeopleB.h 不变

PeopleB.m

	#import "PeopleB.h"

	#if TARGET_IPHONE_SIMULATOR
	#import <objc/objc-runtime.h>
	#else
	#import <objc/runtime.h>
	#import <objc/message.h>
	#endif

	@implementation PeopleB

	// 第一步：我们不动态添加方法，返回NO，进入第二步；
	+ (BOOL)resolveInstanceMethod:(SEL)sel
	{
	    return NO;
	}

	// 第二部：我们不指定备选对象响应aSelector，进入第三步；
	- (id)forwardingTargetForSelector:(SEL)aSelector
	{
	    return nil;
	}

	// 第三步：返回方法选择器，然后进入第四部；
	- (NSMethodSignature *)methodSignatureForSelector:(SEL)aSelector
	{
	    if ([NSStringFromSelector(aSelector) isEqualToString:@"sing"]) {
	        return [NSMethodSignature signatureWithObjCTypes:"v@:"];
	    }
	    
	    return [super methodSignatureForSelector:aSelector];
	}

	// 第四部：这步我们修改调用方法
	- (void)forwardInvocation:(NSInvocation *)anInvocation
	{
	    [anInvocation setSelector:@selector(dance)];
	    // 这还要指定是哪个对象的方法
	    [anInvocation invokeWithTarget:self];
	}

	// 若forwardInvocation没有实现，则会调用此方法
	- (void)doesNotRecognizeSelector:(SEL)aSelector
	{
	    NSLog(@"消息无法处理：%@", NSStringFromSelector(aSelector));
	}

	- (void)dance
	{
	    NSLog(@"跳舞！！！come on！");
	}


	@end

main.m

![](http://7xt1bu.com1.z0.glb.clouddn.com/20.png)


挺复杂的 runtime 是oc中比较难理解的一块了，慢慢来
jspath动态修复，json转model第三方库的核心都是 runtime 。