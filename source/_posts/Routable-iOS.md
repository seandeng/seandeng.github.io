---
title: Routable-iOS
date: 2016-04-17 18:06:02
tags:
---
我们在iOS中通常都是用 pushViewController 或者 presentViewController 的方式来做视图间的跳转，但是这种方式免不了要在一个controller中初始化另外一个controller，使用Routable可以实现controller之间的解耦。

这是routable-ios的github地址：https://github.com/clayallsopp/routable-ios
<!--more-->
在Xcode中新建一个项目 RoutableStudy
## 用cocospods来引入这个库吧，在Pod文件中加入
	platform :ios, '7.0'
	pod 'Routable','~> 0.1.1'
然后 
	pod install

## 把 main interface置空,不使用storyboard
![](http://7xt1bu.com2.z0.glb.clouddn.com/6.png)

分别新建两个UIViewController:   删除默认的ViewController, 新建 FirstViewController 和 SecondViewController

## 在AppDelegate.m 中加入
``` objc
#import <Routable/Routable.h>
#import "FirstViewController.h"
#import "SecondViewController.h"
```

在 - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {  }  中加入
``` objc
self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
// Override point for customization after application launch.
self.window.backgroundColor = [UIColor whiteColor];

UINavigationController *nav = [[UINavigationController alloc] initWithNibName:nil bundle:nil];
[[Routable sharedRouter] map:@"firstViewController" toController:[FirstViewController class]];
[[Routable sharedRouter] map:@"secondViewController" toController:[SecondViewController class] withOptions:[[UPRouterOptions modal] withPresentationStyle:UIModalPresentationFormSheet]];
[[Routable sharedRouter] setNavigationController:nav];

[self.window setRootViewController:nav];
[self.window makeKeyAndVisible];

[[Routable sharedRouter] open:@"firstViewController"];
```

### FirstViewController
``` objc
- (id)initWithRouterParams:(NSDictionary *)params {
    if ((self = [self initWithNibName:nil bundle:nil])) {
        self.title = @"FirstViewController";
    }
    return self;
}

- (void)viewDidLoad {
    [super viewDidLoad];
    
    UIButton *modal = [UIButton buttonWithType:UIButtonTypeRoundedRect];
    [modal setTitle:@"SecondViewController" forState:UIControlStateNormal];
    [modal addTarget:self action:@selector(tapped:) forControlEvents:UIControlEventTouchUpInside];
    [modal sizeToFit];
    [modal setFrame:CGRectMake(0, self.view.bounds.size.height - modal.frame.size.height, modal.frame.size.width, modal.frame.size.height)];
    
    [self.view addSubview:modal];
}

- (void)tapped:(id)sender {
    [[Routable sharedRouter] open:@"secondViewController"];
}
```
### SecondViewController
``` objc
- (id)initWithRouterParams:(NSDictionary *)params {
    if ((self = [self initWithNibName:nil bundle:nil])) {
        self.title = @"SecondViewController";
    }
    return self;
}

- (void)viewDidLoad {
    [super viewDidLoad];
    
    UIButton *modal = [UIButton buttonWithType:UIButtonTypeRoundedRect];
    [modal setTitle:@"Close" forState:UIControlStateNormal];
    [modal addTarget:self action:@selector(tapped:) forControlEvents:UIControlEventTouchUpInside];
    [modal sizeToFit];
    [modal setFrame:CGRectMake(0, self.view.bounds.size.height - modal.frame.size.height, modal.frame.size.width, modal.frame.size.height)];
    [self.view addSubview:modal];
    
    UIButton *user = [UIButton buttonWithType:UIButtonTypeRoundedRect];
    [user setTitle:@"FirstViewController" forState:UIControlStateNormal];
    [user addTarget:self action:@selector(tappedUser:) forControlEvents:UIControlEventTouchUpInside];
    [user sizeToFit];
    [user setFrame:CGRectMake(self.view.bounds.size.width - user.frame.size.width , self.view.bounds.size.height - user.frame.size.height, user.frame.size.width, user.frame.size.height)];
    
    [self.view addSubview:user];
}

- (void)tapped:(id)sender {
    [[Routable sharedRouter] pop];
}

- (void)tappedUser:(id)sender {
    [[Routable sharedRouter] open:@"firstViewController"];
}
```
跑一下程序吧，controller之间的跳转不再需要引入其它的controller并且alloc了。
Routable 不只是只有解偶的作用  先到这里吧 有更多领悟再来补充。