---
title: SocketRocket
date: 2016-11-06 07:43:29
tags:
---

websocket 实现了 客户端和服务器 一次握手 就可以不断接受服务器消息的功能
<!--more-->
具体原理可以看这里[WebSocket 是什么原理？](https://www.zhihu.com/question/20215561)
websocket是http协议上的升级  好像ws前缀是http,wss前缀的就是https了。
facebook出了一个websocket的开源库 挺好用。下面是使用方法:

### pod

	pod 'SocketRocket', '~> 0.4.2'

### import
	#import "SRWebSocket.h"

### 初始化
	- (void)Reconnect{
	    self.webSocket.delegate = nil;
	    [self.webSocket close];

	    self.webSocket = [[SRWebSocket alloc] initWithURLRequest:[NSURLRequest requestWithURL:[NSURL URLWithString:@"ws://echo.websocket.org"]]];
	    self.webSocket.delegate = self;

	    self.title = @"Opening Connection...";

	    [self.webSocket open];
	}

	- (void)viewWillAppear:(BOOL)animated{
	    [self Reconnect];
	}

	- (void)viewDidDisappear:(BOOL)animated{
	    // Close WebSocket
	    self.webSocket.delegate = nil;
	    [self.webSocket close];
	    self.webSocket = nil;
	}

### 实现代理方法
	#pragma mark - SRWebSocketDelegate
	- (void)webSocketDidOpen:(SRWebSocket *)webSocket{
	    NSLog(@"Websocket Connected");
	    self.title = @"Connected!";
	}

	- (void)webSocket:(SRWebSocket *)webSocket didFailWithError:(NSError *)error{
	    NSLog(@":( Websocket Failed With Error %@", error);

	    self.title = @"Connection Failed! (see logs)";
	    self.webSocket = nil;
	}

	- (void)webSocket:(SRWebSocket *)webSocket didReceiveMessage:(id)message{
	    xxx
	}


这样就能不停的重服务器获取信息，而且只需要发送一次请求。
很适合消息通知类的功能。
