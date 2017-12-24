---
title: RACSignal 信号队列 和 信号刺激条件
date: 2016-08-21 23:12:09
tags:
---

信号队列 和 信号刺激条件

<!--more-->

### 信号队列

	//创建3个信号来模拟队列
	RACSignal *signalB = [RACSignal createSignal:^RACDisposable *(id subscriber) {
	    [subscriber sendNext:@"B"];
	    [subscriber sendCompleted];
	    return nil;
	}];
	    
	RACSignal *signalC = [RACSignal createSignal:^RACDisposable *(id subscriber) {
	    [subscriber sendNext:@"C"];
	    [subscriber sendCompleted];
	    return nil;
	}];
	RACSignal *signalD = [RACSignal createSignal:^RACDisposable *(id subscriber) {
	    [subscriber sendNext:@"D"];
	    [subscriber sendCompleted];
	    return nil;
	}];
	//连接组队列:将几个信号放进一个组里面,按顺序连接每个,每个信号必须执行sendCompleted方法后才能执行下一个信号
	RACSignal *signalGroup = [[signalB concat:signalC] concat:signalD];
	    [signalGroup subscribeNext:^(id x) {
	    NSLog(@"%@",x);
	}];


    //调用这个方法可以不用写sendCompleted
	[[RACSignal merge:@[signalB,signalC,signalD]] subscribeNext:^(id x) {
        NSLog(@"%@",x);
    }];


结果：

2016-08-21 23:14:35.786 RAC[83044:2660021] B
2016-08-21 23:14:35.787 RAC[83044:2660021] C
2016-08-21 23:14:35.787 RAC[83044:2660021] D


### 信号刺激的条件设置

	//创建一个信号
	[[[RACSignal createSignal:^RACDisposable *(id subscriber) {
	    //创建一个定时信号，每隔1秒刺激一次信号
	    [[RACSignal interval:1 onScheduler:[RACScheduler mainThreadScheduler]] subscribeNext:^(id x) {
	        [subscriber sendNext:@"直到接收到信号B才结束"];
	        }];
	    return nil;
	    //直到此情况下停止刺激信号
	}] takeUntil:[RACSignal createSignal:^RACDisposable *(id subscriber) {
	        dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(3 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
	            NSLog(@"发送信号B");
	            [subscriber sendNext:@"发送信号B"];
	            });
	        return nil;
	}]] subscribeNext:^(id x) {
	            NSLog(@"%@", x);
	}];

结果：

2016-08-21 23:17:15.064 RAC[83081:2662040] 直到接收到信号B才结束
2016-08-21 23:17:16.066 RAC[83081:2662040] 直到接收到信号B才结束
2016-08-21 23:17:17.066 RAC[83081:2662040] 直到接收到信号B才结束
2016-08-21 23:17:17.067 RAC[83081:2662040] 发送信号B
