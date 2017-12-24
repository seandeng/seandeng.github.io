---
title: iOS10推送通知适配
date: 2016-09-23 23:37:22
tags:
---

碰到了一个推送问题，iOS10系统, app在后台的时候，点击通知只能进app，无法跳转页面。
debug后发现连方法都没有进。
<!--more-->
网上查了下,iOS10推送新增了UserNotifications Framework，使用起来其实很简单。
只是在iOS10以上系统上点击通知栏，回调方法不再走原来的这两个方法

	- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
	}
	- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler {
	}

而是在前台的时候回调(实测，没写这个方法，在前台也会调用之前版本的方法)

	- (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions))completionHandler

从后台进入的时候回调(在后台就必须用这个了)

	- (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void (^)())completionHandler

### 导入头文件

	#import <UserNotifications/UserNotifications.h/>

### 注册通知

	#define IOS8 ([[UIDevice currentDevice].systemVersion doubleValue] >= 8.0 && [[UIDevice currentDevice].systemVersion doubleValue] < 9.0)
	#define IOS8_10 ([[[UIDevice currentDevice] systemVersion] floatValue] >= 8.0 && [[UIDevice currentDevice].systemVersion doubleValue] < 10.0)
	#define IOS10 ([[[UIDevice currentDevice] systemVersion] floatValue] >= 10.0)

	- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions 方法中

	if (IOS10) {
		UNUserNotificationCenter *center = [UNUserNotificationCenter currentNotificationCenter];
		center.delegate = self;
		[center requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {
		    if (!error) {
		        NSLog(@"succeeded!");
		    }
		}];
	} else if (IOS8_10){//iOS8-iOS10
		UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeAlert | UIUserNotificationTypeSound) categories:nil];
		[application registerUserNotificationSettings:settings];
		[application registerForRemoteNotifications];
	} else {//iOS8以下
		[application registerForRemoteNotificationTypes: UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeSound];
	}

### 回调方法中，获取通知数据

	- (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void (^)())completionHandler {
	    NSDictionary *userInfo = response.notification.request.content.userInfo;
	 　　//消息处理
	}

对于本地通知没有什么变化依然会回调

	-(void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification

iOS10 的通知被大幅度的增强了，有了更好的交互性，不过从iOS7~iOS10的通知适配需要 根据版本来获取权限、调用方法。

