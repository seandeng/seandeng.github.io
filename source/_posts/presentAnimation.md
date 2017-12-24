---
title: 转场动画01 UIViewControllerTransitioning
date: 2016-09-17 13:31:37
tags:
---

### UIViewControllerTransitioning
<!--more-->
创建一个类叫做 RotationPresentAnimation 继承于 NSObject 并实现了协议 UIViewControllerAnimatedTransitioning。

这个协议负责切换的具体内容，也即“切换中应该发生什么”。开发者在做自定义切换效果时大部分代码会是用来实现这个接口。 这个协议只有两个方法：

	//返回动画的时间
	- (NSTimeInterval)transitionDuration:(nullable id <UIViewControllerContextTransitioning>)transitionContext; 
	//在进行切换的时候将调用该方法，我们对于切换时的UIView的设置和动画都在这个方法中完成。
	- (void)animateTransition:(id <UIViewControllerContextTransitioning>)transitionContext;   

实现这两个方法

	- (NSTimeInterval)transitionDuration:(id <UIViewControllerContextTransitioning>)transitionContext{
	    return 0.5f;
	}

	- (void)animateTransition:(id <UIViewControllerContextTransitioning>)transitionContext{
	    //1
	    UIViewController *toVC = [transitionContext viewControllerForKey:UITransitionContextToViewControllerKey];

	    //2
	    CGRect finalRect = [transitionContext finalFrameForViewController:toVC];

	    //下面这句话的意思是在 finalRect 的基础上获取一个 x 偏移0 y偏移一个屏幕高度的 frame。
	    toVC.view.frame = CGRectOffset(finalRect, 0, [[UIScreen mainScreen]bounds].size.height);

	    //3
	    [[transitionContext containerView]addSubview:toVC.view];

	    //4
	    [UIView animateWithDuration:[self transitionDuration:transitionContext] delay:0.0 usingSpringWithDamping:0.6 initialSpringVelocity:0.0 options:UIViewAnimationOptionCurveLinear animations:^{
	        toVC.view.frame = finalRect;
	    } completion:^(BOOL finished) {
	        //5
	        [transitionContext completeTransition:YES];
	    }];
	}

1、我们需要得到参与切换的两个ViewController的信息，使用context的方法拿到它们的参照； 
2、对于要呈现的VC，我们希望它从屏幕下方出现，因此将初始位置设置到屏幕下边缘； 
3、将view添加到containerView中； 
4、开始动画。这里的动画时间长度和切换时间长度一致。usingSpringWithDamping的UIView动画API是iOS7新加入的，描述了一个模拟弹簧动作的动画曲线； 
5、在动画结束后我们必须向context报告VC切换完成，是否成功。系统在接收到这个消息后，将对VC状态进行维护。


### UIViewControllerTransitioningDelegate

这个接口的作用比较单一，在需要VC切换的时候系统会向实现了这个接口的对象询问是否需要使用自定义的切换效果。

所以，一个比较好的地方是直接在主控制器 ViewController 中实现这个协议。

	@interface ViewController ()<PresentedVCDelegate,UIViewControllerTransitioningDelegate>

	@property(nonatomic,strong)RotationPresentAnimation *presentAnimation;

	@end

	...

	//1
	-(instancetype)initWithCoder:(NSCoder *)aDecoder{
	    self = [super initWithCoder:aDecoder];
	    if (self) {
	        self.presentAnimation = [[RotationPresentAnimation alloc]init];
	    }
	    return self;
	}

	-(void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender{

	    ...
	    //2
	    pvc.transitioningDelegate = self;
	    ...

	}

	//3
	- (id <UIViewControllerAnimatedTransitioning>)animationControllerForPresentedController:(UIViewController *)presented presentingController:(UIViewController *)presenting sourceController:(UIViewController *)source{
	    return self.presentAnimation;

	}


