---
title: iOS 蓝牙检测
date: 2016-07-10 12:38:49
tags:
---
## 蓝牙

蓝牙是iOS各个权限中接口比较友好的一个，给了开启关闭实时监测的delegate，实现后就能方便的监测蓝牙状态了
<!--more-->
之前的通知、healthkit、地理位置、运动处理器 之类的权限一旦选择后就不得不去设置中打开，而且监控状态复杂。但蓝牙可以在iPhone手机中在 从底部控制菜单中 开启，并且监测十分简单。
说一下蓝牙监测的步骤:

### 引入头文件
	#import <CoreBluetooth/CoreBluetooth.h>
	#import <CoreBluetooth/CBService.h>

### 添加属性
	@interface PXWeightBindViewController ()<CBCentralManagerDelegate>

	@property (nonatomic,strong) CBCentralManager* centralManager;

	@end



### 初始化
	 _centralManager = [[CBCentralManager alloc] initWithDelegate:self queue:nil];


### 实现代理方法
	- (void)centralManagerDidUpdateState:(CBCentralManager *)central
	{
	    if (central.state == CBCentralManagerStatePoweredOff){
	         [self.rader stopAnimation];
	         self.stateLabel.text = @"停止搜索...";
	        self.tipLabel.text = @"请打开蓝牙";
	        NSLog(@"蓝牙未开启");
	    }
	    else if (central.state == CBCentralManagerStatePoweredOn){
	         [self.rader startAnimation];
	         self.stateLabel.text = @"正在搜索...";
	        self.tipLabel.text = @"请拿着手机站在秤上";
	        NSLog(@"蓝牙已打开");
	    }
	    else if (central.state == CBCentralManagerStateUnknown){
	        [self.rader stopAnimation];
	        NSLog(@"未知");
	    }
	    else if(central.state == CBCentralManagerStateUnsupported){
	         [self.rader stopAnimation];
	        self.tipLabel.text = @"不支持蓝牙";
	        NSLog(@"不支持蓝牙");
	    }
	}

然后就可以配合label做下图的效果了:

<img src="http://7xt1bu.com1.z0.glb.clouddn.com/21.png" width="375" height="667" />
<img src="http://7xt1bu.com1.z0.glb.clouddn.com/22.png" width="375" height="667" />



### RAC

现在的蓝牙智能设备接入，一般提供sdk，在使用中可以在不同状态时赋值变量，然后
可以在controller中通过RAC来改变UI状态，告诉用户是否绑定了蓝牙或者是处于测量中
	
	[RACObserve(manager, isConnect) subscribeNext:^(id x) {
        if ([x boolValue] ) {
            
            self.qingNiuView.testingLabel.hidden = NO;
            self.qingNiuView.unbindButton.hidden = YES;
            
        }else{
            
            self.qingNiuView.testingLabel.hidden = YES;
            self.qingNiuView.unbindButton.hidden = NO;
        }
    }];
