---
title: Objective-C Runtime 基础
date: 2016-06-18 12:55:46
tags: Runtime
---

## Objective-C Runtime 是什么

Objective-C Runtime是一个将C语言转化为面向对象语言的扩展
C++和Objective-C都是在C的基础上加入面向对象的特性扩充而成的程序设计语言，但二者实现的机制差异很大
<!--more-->

C++是基于静态类型，而Objective-C是基于动态运行时类型
也就是说用C++编写的程序编译时就直接编译成了可令机器读懂的机器语言；用Objective-C编写的程序不能直接编译成可令机器读懂的机器语言，而是在程序运行的时候，通过Runtime把程序转为可令机器读懂的机器语言

所以啊，想学好Objective-C,还是要学好C。 

## Objective-C的消息传递

在 Objective-C 中，对象调用方法叫做发送消息。在编译时，程序的源代码就会从对象发送消息转换成Runtime的objc_msgSend函数调用。
例如某实例变量receiver实现某一个方法oneMethod

	[receiver oneMethod];

Runtime会将其转成类似这样的代码

	objc_msgSend(receiver, selector);

具体会转换成什么代码呢？
Runtime会根据类型自动转换成下列某一个函数：
objc_msgSend:普通的消息都会通过该函数发送
objc_msgSend_stret:消息中有数据结构作为返回值（不是简单值）时，通过此函数发送和接收返回值
objc_msgSendSuper:和objc_msgSend类似，这里把消息发送给父类的实例
objc_msgSendSuper_stret:和objc_msgSend_stret类似，这里把消息发送给父类的实例并接收返回值
当消息被发送到实例对象时，是如图所示处理的

![](http://7xt1bu.com1.z0.glb.clouddn.com/16.png)

objc_msgSend函数的调用过程：

第一步：检测这个selector是不是要忽略的。
第二步：检测这个target是不是nil对象。nil对象发送任何一个消息都会被忽略掉。
第三步：
1.调用实例方法时，它会首先在自身isa指针指向的类（class）methodLists中查找该方法，如果找不到则会通过class的super_class指针找到父类的类对象结构体，然后从methodLists中查找该方法，如果仍然找不到，则继续通过super_class向上一级父类结构体中查找，直至根class；
2.当我们调用某个某个类方法时，它会首先通过自己的isa指针找到metaclass，并从其中methodLists中查找该类方法，如果找不到则会通过metaclass的super_class指针找到父类的metaclass对象结构体，然后从methodLists中查找该方法，如果仍然找不到，则继续通过super_class向上一级父类结构体中查找，直至根metaclass；
第四步：前三部都找不到就会进入动态方法解析(看下文)。

## 消息动态解析

动态解析流程图

![](http://7xt1bu.com1.z0.glb.clouddn.com/17.png)

请参照图片品味以下步骤

第一步：通过resolveInstanceMethod：方法决定是否动态添加方法。如果返回Yes则通过class_addMethod动态添加方法，消息得到处理，结束；如果返回No，则进入下一步；
第二步：这步会进入forwardingTargetForSelector:方法，用于指定备选对象响应这个selector，不能指定为self。如果返回某个对象则会调用对象的方法，结束。如果返回nil，则进入第三部；
第三部：这步我们要通过methodSignatureForSelector:方法签名，如果返回nil，则消息无法处理。如果返回methodSignature，则进入下一步；
第四部：这步调用forwardInvocation：方法，我们可以通过anInvocation对象做很多处理，比如修改实现方法，修改响应对象等，如果方法调用成功，则结束。如果失败，则进入doesNotRecognizeSelector方法，若我们没有实现这个方法，那么就会crash。

## Runtime 例子1

`class_getInstanceVariable`: 获取一个实例的成员变量值
`objc_allocateClassPair`: 创建一个类
`class_addIvar`: 为一个类添加成员变量
`sel_registerName`: 注册方法
`class_addMethod`: 添加方法
`objc_registerClassPair`: 注册该类
`object_setIvar`: 为实例的成员变量赋值
`objc_disposeClassPair`: 销毁类


	#if TARGET_IPHONE_SIMULATOR
	#import <objc/objc-runtime.h>
	#else
	#import <objc/runtime.h>
	#import <objc/message.h>
	#endif

	#import <Foundation/Foundation.h>

	// 自定义一个方法
	void sayFunction(id self, SEL _cmd, id some) {
	    NSLog(@"%@岁的%@说：%@", object_getIvar(self, class_getInstanceVariable([self class],"_age")),
	                          [self valueForKey:@"name"],
	                          some);
	}
	int main(int argc, const char * argv[]) {
	    @autoreleasepool {
	        
	        // 动态创建对象 创建一个Person 继承自 NSObject类
	        Class People = objc_allocateClassPair([NSObject class], "Person", 0);
	        
	        // 为该类添加NSString *_name成员变量
	        class_addIvar(People, "_name", sizeof(NSString*), log2(sizeof(NSString*)), @encode(NSString*));
	        // 为该类添加int _age成员变量
	        class_addIvar(People, "_age", sizeof(int), sizeof(int), @encode(int));
	        
	        // 注册方法名为say的方法
	        SEL s = sel_registerName("say:");
	        // 为该类增加名为say的方法
	        class_addMethod(People, s, (IMP)sayFunction, "v@:@");
	        
	        // 注册该类
	        objc_registerClassPair(People);
	        
	        // 创建一个类的实例
	        id peopleInstance = [[People alloc] init];
	        
	        // KVC 动态改变 对象peopleInstance 中的实例变量
	        [peopleInstance setValue:@"小王" forKey:@"name"];
	        
	        // 从类中获取成员变量Ivar
	        Ivar ageIvar = class_getInstanceVariable(People, "_age");
	        // 为peopleInstance的成员变量赋值
	        object_setIvar(peopleInstance, ageIvar, @18);
	        
	        // 调用 peopleInstance 对象中的 s 方法选择器对于的方法
	        // objc_msgSend(peopleInstance, s, @"大家好!"); // 这样写也可以，请看我博客说明
	        ((void (*)(id, SEL, id))objc_msgSend)(peopleInstance, s, @"大家好");
	        
	        peopleInstance = nil; //当People类或者它的子类的实例还存在，则不能调用objc_disposeClassPair这个方法；因此这里要先销毁实例对象后才能销毁类；
	        
	        // 销毁类
	        objc_disposeClassPair(People);
	        
	    }
	    return 0;
	}

运行打印的值是 

	Runtime[2150:138928] 18岁的小王说：大家好

动态创建一个类，并创建成员变量和方法，最后赋值成员变量并发送消息。其中成员变量的赋值使用了KVC和object_setIvar函数两种方式
