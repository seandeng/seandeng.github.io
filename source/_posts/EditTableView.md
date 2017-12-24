---
title: 自定义 UITableView 的编辑模式
date: 2016-12-08 22:45:18
tags:
---

UITableView 的编辑模式下，左滑一个cell，会看到一个红底白字的删除，如果要简单的修改这个样式，可以采取下面这样的方法。
直接修改subview，快速有效。

<!--more-->

	-(void)layoutSubviews{
	    [super layoutSubviews];
	    
	    for (UIView *subView in self.subviews) {
	        if ([subView isKindOfClass:NSClassFromString(@"UITableViewCellDeleteConfirmationView")]) {
	            subView.backgroundColor = [UIColor clearColor];
	            for (UIButton *btn in subView.subviews) {
	                if ([btn isKindOfClass:[UIButton class]]) {
	                    [btn setTitle:@"" forState:UIControlStateNormal];
	                    [btn setTitle:@"" forState:UIControlStateHighlighted];
	                    [btn setImage:[UIImage imageNamed:@"my_collect"] forState:UIControlStateNormal];
	                    btn.backgroundColor=[UIColor clearColor];
	                }
	            }
	        }
	    }
	}
