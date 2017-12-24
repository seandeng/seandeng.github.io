---
title: CLLocationManager 获取 GPS 位置信息
date: 2016-08-07 16:13:46
tags:
---

使用 CLLocationManager 可以获取 GPS 位置信息，比如经度，纬度，海拔高度等. 基本步骤如下:
<!--more-->
#### 加入 CoreLocation.framework , 导入头文件。

	#import <CoreLocation/CoreLocation.h>

#### 初始化，并开始定位

	locationManager = [[CLLocationManager alloc] init];
	locationManager.delegate = self;
	locationManager.desiredAccuracy = kCLLocationAccuracyBest; // 越精确，越耗电！
    
    // Movement threshold for new events.
    self.locationManager.distanceFilter = 10; // 每10米寻求一次定位

    //activityType：设置定位数据的用途。
    //该属性支持CLActivityTypeOther（定位数据作为普通用途）、
    //CLActivityTypeAutomotiveNavigation（定位数据作为车辆导航使用）、
    //CLActivityTypeFitness（定位数据作为步行导航使用）和CLActivityTypeOtherNavigation（定位数据作为其他导航使用）这几个枚举值之一。
    self.locationManager.activityType = CLActivityTypeFitness;

	[locationManager startUpdatingLocation]; // 开始定位


#### 在 CLLocationManagerDelegate 中获取 GPS 位置信息。

	- (void)locationManager:(CLLocationManager *)manager didUpdateToLocation:(CLLocation *)newLocation fromLocation:(CLLocation *)oldLocation
	{
	    NSLog(@"oldLocation.coordinate.timestamp:%@", oldLocation.timestamp);
	    NSLog(@"oldLocation.coordinate.longitude:%f", oldLocation.coordinate.longitude);
	    NSLog(@"oldLocation.coordinate.latitude:%f", oldLocation.coordinate.latitude);
	    
	    NSLog(@"newLocation.coordinate.timestamp:%@", newLocation.timestamp);
	    NSLog(@"newLocation.coordinate.longitude:%f", newLocation.coordinate.longitude);
	    NSLog(@"newLocation.coordinate.latitude:%f", newLocation.coordinate.latitude);
	    
	    NSTimeInterval interval = [newLocation.timestamp timeIntervalSinceDate:oldLocation.timestamp];
	    NSLog(@"%lf", interval);
	    
	    // 取到精确GPS位置后停止更新
	    if (interval < 3) {
	        // 停止更新
	        [locationManager stopUpdatingLocation];
	    }
	    
	    latitudeLabel.text = [NSString stringWithFormat:@"%f", newLocation.coordinate.latitude];
	    longitudeLabel.text = [NSString stringWithFormat:@"%f", newLocation.coordinate.longitude];
	}

#### 其它
一般一个跑步类app，能做到轨迹距离准确，语音播报清晰，就是一个优秀的app了。下图就是咕咚在app的优化中的效果，锯齿状的轨迹变成了圆，并且重合度很高，拿着自己app去跑圈就是最好的测试了。

![](http://7xt1bu.com1.z0.glb.clouddn.com/28.jpg)

补充一个raywenderlich上很好的教程，如果要尝试做一个这样的app那可以根据这个教程入个门：

[How To Make an App Like RunKeeper: Part 1](https://www.raywenderlich.com/73984/make-app-like-runkeeper-part-1)
[How To Make an App Like RunKeeper: Part 2](https://www.raywenderlich.com/74331/make-app-like-runkeeper-part-2)






