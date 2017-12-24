---
title: UIScrollvie 里 keyboard遮挡问题
date: 2016-11-24 23:33:21
tags:
---

防止键盘遮挡的方式有很多，在一个UIScrollView 中，可以进行如下处理。
关键是拿到textfield的bottom，然后和键盘通知里的键盘高度做计算。
<!--more-->
	#define ScreenWidth         [[UIScreen mainScreen] bounds].size.width
	#define ScreenHeight        [[UIScreen mainScreen] bounds].size.height
	#import "ViewController.h"
	#import "UIView+Positioning.h"
	@interface ViewController ()<UIScrollViewDelegate,UITextViewDelegate>
	    
	@property (nonatomic,strong) UIScrollView *scrollView;
	    
	@property (nonatomic,strong) UITextView *textView;

	@end

	@implementation ViewController

	- (void)viewDidLoad {
	    [super viewDidLoad];
	    // Do any additional setup after loading the view, typically from a nib.
	    [self.view addSubview:self.scrollView];
	    
	    
	    _textView = [[UITextView alloc]initWithFrame:CGRectMake(0, 400, ScreenWidth, 50)];
	    _textView.backgroundColor = [UIColor lightGrayColor];
	    _textView.delegate =self;
	    [self.scrollView addSubview:_textView];
	    
	    
	    UITapGestureRecognizer  *tapGR=[[UITapGestureRecognizer alloc]initWithTarget:self action:@selector(tap)];
	    tapGR.numberOfTapsRequired=1;
	    [self.view addGestureRecognizer:tapGR];
	}
	    
	-(void)tap{
	    [_textView resignFirstResponder];
	}
	    
	- (void)viewWillAppear:(BOOL)animated
	{
	    [super viewWillAppear:animated];
	    
	    [[NSNotificationCenter defaultCenter] addObserver:self
	                                             selector:@selector(keyboardWillShow:)
	                                                 name:UIKeyboardWillShowNotification
	                                               object:nil];
	    [[NSNotificationCenter defaultCenter] addObserver:self
	                                             selector:@selector(keyboardWillShow:)
	                                                 name:UIKeyboardWillHideNotification
	                                               object:nil];
	}
	    
	- (void)viewDidDisappear:(BOOL)animated
	{
	    [super viewDidDisappear:animated];
	        
	    [[NSNotificationCenter defaultCenter] removeObserver:self
	                                                        name:UIKeyboardWillHideNotification
	                                                      object:nil];
	    [[NSNotificationCenter defaultCenter] removeObserver:self
	                                                        name:UIKeyboardWillShowNotification
	                                                      object:nil];
	}
	    
	- (void)keyboardWillShow:(NSNotification *)aNotification
	{
	    CGFloat contentOffY = self.scrollView.contentOffset.y;
	    UIViewAnimationCurve curve = [aNotification.userInfo[UIKeyboardAnimationCurveUserInfoKey]integerValue];
	    NSNumber *duration = aNotification.userInfo[UIKeyboardAnimationDurationUserInfoKey];
	     CGSize size = [aNotification.userInfo[UIKeyboardFrameEndUserInfoKey] CGRectValue].size;
	    float keyboardHeight = size.height;
	    CGFloat spaceY = self.textView.bottom - (ScreenHeight-keyboardHeight)-contentOffY;
	    [UIView animateWithDuration:[duration doubleValue] delay:0 options:(curve << 16) animations: ^{
	        if (spaceY>0) {
	            CGFloat space =_scrollView.contentOffset.y + spaceY;
	            [_scrollView setContentOffset:CGPointMake(0, space) animated:YES];
	        }
	        
	    } completion:^(BOOL finished) {
	    }];
	}
	    
	-(UIScrollView *)scrollView{
	    if (!_scrollView) {
	        _scrollView = [[UIScrollView alloc] initWithFrame:CGRectMake(0, 0, ScreenWidth, ScreenHeight - 64)];
	        _scrollView.contentSize = CGSizeMake(ScreenWidth, 800);
	        _scrollView.delegate = self;
	        _scrollView.alwaysBounceVertical = YES;
	    }
	    return _scrollView;
	}


	- (void)didReceiveMemoryWarning {
	    [super didReceiveMemoryWarning];
	    // Dispose of any resources that can be recreated.
	}


	@end
