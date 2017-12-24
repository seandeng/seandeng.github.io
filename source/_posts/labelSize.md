---
title: UILabel 高度的计算
date: 2016-08-14 21:38:12
tags:
---

## UILabel 高度的计算

autolayout极大的方便了iOS的布局，但是碰到多变的页面，自动布局的方式就不如frame的布局方式了
不过使用frame布局一个多个view存在于UIScrollView中的页面,就需要去计算各个控件的高度。

<!--more-->

### 单行的 UILabel 

比如标题之类的 计算高度可以用 `sizeWithAttributes`

        _titleLabel = ({
            UILabel *label = [[UILabel alloc]init];
            label.text = title;
            label.font = [UIFont systemFontOfSize:15];
            label.textAlignment = NSTextAlignmentLeft;
            label;
        });
        [self  addSubview:_titleLabel];
        
        CGSize titleSize =[title sizeWithAttributes:@{NSFontAttributeName:[UIFont systemFontOfSize:15]}];

### 多行的 UILabel 

需要计算多行，并且随着屏幕宽度自动换行的 UILabel饰 就需要用 `boundingRectWithSize`
        
        _descLabel = ({
            UILabel *label = [[UILabel alloc]init];
            label.text = desc;
            label.font = [UIFont systemFontOfSize:14];
            label.lineBreakMode = NSLineBreakByWordWrapping;
            label.numberOfLines = 0;
            label.textColor = [UIColor px_lightGray];
            label;
        });
        
        [self addSubview:_descLabel];
        
        
        CGSize descSize = [desc boundingRectWithSize:CGSizeMake(self.width-30, MAXFLOAT) options:NSStringDrawingUsesLineFragmentOrigin attributes:@{NSFontAttributeName:[UIFont systemFontOfSize:14]} context:nil].size;

使用这两个方法的时候传入的参数 如字体样式 大小都要和创建的UILabel 一致才能计算准确
最后将 它们的高度和距离相加就是整个view的高度了。


## UIProgressView 高度控制

补充纪录下 使用UIProgressView的时候 发现居然无法用frame来控制高度，只能控制宽度。
但是用autolayout就可以方便的控制高度了
查了很多资料 最后的解决方案类似html中的div的概念，先定义好一个区块，然后在区块中使用约束。

### 先用frame的方式定义好一个区块
	_containView = ({
	            UIView *view = [[UIView alloc]init];
	            view;
	        });
	[self addSubview:_containView]


	-(void)layoutSubviews{
	    [super layoutSubviews];
	    _containView.frame = CGRectMake(40, _descLabel.bottom+80, self.width - 80, 6);
	}
### 然后在区块中使用autolayout布局
	_progressView = ({
            UIProgressView *progressView = [[UIProgressView alloc]init];
            progressView.trackTintColor= [[UIColor blackColor] colorWithAlphaComponent:0.3]; //背景颜色
            
            if (!progressIsGood) {
                progressView.progressTintColor = [UIColor colorWithRed:1.00 green:0.56 blue:0.42 alpha:1.00]; //进度条颜色
            }else{
                progressView.progressTintColor = [UIColor px_greenColor]; //进度条颜色
            }
            
            progressView.progress = progress;
            progressView.layer.masksToBounds = YES;
            progressView.layer.cornerRadius = 3;
            progressView;
        });
    [_containView addSubview:_progressView];
    [_progressView autoPinEdgesToSuperviewEdges];