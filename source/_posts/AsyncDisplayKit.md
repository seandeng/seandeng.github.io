---
title: AsyncDisplayKit学习
date: 2016-04-23 21:23:59
tags: AsyncDisplayKit
---
## 1.介绍
AsyncDisplayKit 是facebook出的iOS框架，主要用来加强复杂的用户交互界面顺畅度和减少响应时间。

这个东西的效果要去旧一些的设备上才看得出来，多用在比较复杂的UITableview上, 只是普通的需求文字图片排版其实没必要。

它设计出来的初衷是为了开发facebook 的 [Paper](https://www.facebook.com/paper#)(链接需科学上网)。


<!--more-->

## 2.集成：

	pod 'AsyncDisplayKit'

如果你使用swift那么要在你的 [Objective-C bridging header](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/BuildingCocoaApps/MixandMatch.html)中加入

	#import <AsyncDisplayKit/AsyncDisplayKit.h>

AsyncDisplayKit（ASDK）的基本单元是node，ASDisplayNode是UIView之上的抽象层，同时也是CALayer的抽象层。和只能被用在主线程的视图不同，nodes是线程安全的：你能并行的实例化并设置整个node层级，并且在后台线程里运行。


## 3.使用：
objc:

``` objc
dispatch_async(_backgroundQueue, ^{
  ASTextNode *node = [[ASTextNode alloc] init];
  node.attributedString = [[NSAttributedString alloc] initWithString:@"hello!"
                                                          attributes:nil];
  [node measure:CGSizeMake(screenWidth, FLT_MAX)];
  node.frame = (CGRect){ CGPointZero, node.calculatedSize };

  // self.view isn't a node, so we can only use it on the main thread
  dispatch_async(dispatch_get_main_queue(), ^{
    [self.view addSubview:node.view];
  });
});
```

swift:

``` swift
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0) {
  let node = ASTextNode()
  node.attributedString = NSAttributedString(string: "hello")
  node.measure(CGSize(width: screenWidth, height: CGFloat.max))
  node.frame = CGRect(origin: CGPointZero, size: node.calculatedSize)

  // self.view isn't a node, so we can only use it on the main thread
  dispatch_async(dispatch_get_main_queue()) {
   self.view.addSubview(node.view)
  }
}
```

## 4.去构建一个自己的ASCellNode

首先你要继承 ASCellNode

然后添加自己的node或者官方的组件
``` objc
@interface PXStatusCellNode : ASCellNode
{
    @protected
    ASDisplayNode *_contentBackgroudNode;
    ASTextNode *_messageNode;
    ASTextNode *_avatarNode;
    ASImageNode *_photosNode;
}
```

``` objc
计算高度
- (CGSize)calculateSizeThatFits:(CGSize)constrainedSize
{

｝
```

``` objc
布局
- (void)layout
{
   
}
```

然后就可以在 ASTableView 中使用这个自定义的 ASCellNode了 ， 薄荷的朋友圈timeline用的就是这个。


## 5.AsyncDisplayKit包括的组件：

· ASDisplayNode. UIView的副本, 一个子类，用来自定义node。
· ASControlNode. 类似于UIControl, 用来制作按钮的子类。
· ASImageNode. 类似于UIImageView, 异步的解码图像资源。
· ASTextNode. 类似于UITextView, 基于TextKit构建，支持富文本的全部特性。
· ASTableView. UITableView子类，用于支持node。
· ASVideoNode. 刚出的播放视频组件，包装了 AVPlayer, 只有播放和暂停功能，性能还未优化


## 6.官方文档：

· [Read the Getting Started guide](http://asyncdisplaykit.org/docs/getting-started.html)

例子项目每个都可以单独打开 需要运行下 `pod install`
· [Get the sample projects](https://github.com/facebook/AsyncDisplayKit/tree/master/examples)

· [Browse the API reference](http://asyncdisplaykit.org/appledocs.html)



## 7.除了官方文档以外值得一看的教程：

英文：
[asyncdisplaykit-tutorial-achieving-60-fps-scrolling](https://github.com/nixzhu/dev-blog/blob/master/2014-11-22-asyncdisplaykit-tutorial-achieving-60-fps-scrolling.md)
中文：
[AsyncDisplayKit 教程：达到 60 FPS 的滚动帧率](http://www.raywenderlich.com/86365/asyncdisplaykit-tutorial-achieving-60-fps-scrolling)










