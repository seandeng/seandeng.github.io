---
title: ReactiveCocoa 学习笔记
date: 2016-07-31 12:03:07
tags:
---

使用了一阵RAC确实在很多方面非常方便，使程序逻辑更加清晰了。
<!--more-->

比如接受通知的方法直接可以写在block中，RAC中的通知不需要remove observer，因为在rac_add方法中他已经写了remove：

    @weakify(self)
    [[[NSNotificationCenter defaultCenter] rac_addObserverForName:@"NotificationRefreshHotTopic" object:nil] subscribeNext:^(NSNotification *notification) {
        @strongify(self)
        [self.hotCategoryEventsViewController.model forceReload];
        [self.favCategoryEventsViewController.model forceReload];
    }];


对变量的改变赋值变得方便：

        RAC(badgeView, badgeText) = [RACObserve(app.user.currentUser, unReadCount) map: ^id (NSNumber *number) {
            return [number isEqualToNumber:@(0)] ? @"" : [number stringValue];
        }];

对多个接口请求后统一返回的处理也很便捷：

    @weakify(self)
    [[RACSignal combineLatest:@[[self fetchHomeCards], [self fetchDiamongCardStatus],[self fetchMCPeriods]]] subscribeNext: ^(RACTuple *x) {
        
        @strongify(self)
        BINGO_RESP_HOME_ITEMS *cardsResp = x.first;
        STATUS_RESP_CHECKIN_STATUS *statusResp = x.second;
        RECORD_RESP_MC_PERIODS_LATEST *MCPeriods = x.third;
        [self storeSignInStateWithData:x.second];
        [self handleHomeCardsData:cardsResp statusData:statusResp];
        
        self.mcData = [self getMCData:MCPeriods.mc_latest];
        self.mcSummary = MCPeriods.mc_summary;
        
    } error: ^(NSError *error) {
        @strongify(self)
        [[BHLoadingHUD sharedInstance] hidden];
        [self didFailed];
        
    } completed: ^{
        @strongify(self)
        [[BHLoadingHUD sharedInstance] hidden];
        [self didReloaded];
    }];


对一个按钮点击后弹出alert，点击后的逻辑都可以在一段函数里完成。

    _qingNiuView.unbindButton.rac_command = [[RACCommand alloc] initWithSignalBlock:^RACSignal *(id input) {
        
        UIAlertView *alertView = [[UIAlertView alloc] initWithTitle:@"是否解绑智能秤？"
                                                            message:nil
                                                           delegate:nil
                                                  cancelButtonTitle:@"取消"
                                                  otherButtonTitles:@"确定", nil];
        
        [[alertView rac_buttonClickedSignal] subscribeNext:^(NSNumber *buttonIndex) {
            if ([buttonIndex integerValue] == 0) {
                //Do Nothing
            }else{
                [[PXQingNiuManager sharedInstance]unbindDevice];
                
                self.qingNiuView.tryButton.hidden = NO;
                self.qingNiuView.unbindButton.hidden = YES;
                self.qingNiuView.alreadyBindLabel.hidden = YES;
                self.qingNiuView.title.text = @"拥有智能秤";
                self.qingNiuView.subTitle.text = @"体验更便捷的记录方式";
            }
        }];
        
        [alertView show];
        
        return [RACSignal empty];
    }];


RAC是一个值得深入学习的好框架。