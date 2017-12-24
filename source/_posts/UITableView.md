---
title: UITableView 优化
date: 2016-08-28 17:36:23
tags:
---


### 正确地使用UITableViewCell的重用机制

<!--more-->
UITableView的核心的就是 UITableViewCell 的重用机制。UITableView 只会创建一屏幕（或一屏幕多一点）的 UITableViewCell ，每当 cell 滑出屏幕范围时，就会放入到一重用池当中，当要显示新的 cell 时，先去重用池中取，若没有可用的，才会重新创建。这样可以极大的减少内存的开销。

早期的写法

	static NSString *cellID = @"Cell";
	UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:cellID];
	if (!cell) {
	 cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:cellID];
	 //cell 初始化
	}
	// cell 设置数据
	return cell;

或者通过注册cell的方式 这是iOS6后重用的新方法

	//注册cell
	[tableView registerClass:[UITableViewCell class] forCellReuseIdentifier:@"cell"];
	//获取cell    
	UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"cell"];

>Beginning in iOS 6, clients can register a nib or class for each cell.
>Instances returned from the new dequeue method will also be properly sized when they are returned.
	- (void)registerNib:(UINib *)nib forCellReuseIdentifier:(NSString *)identifier NS_AVAILABLE_IOS(5_0);
	- (void)registerClass:(Class)cellClass forCellReuseIdentifier:(NSString *)identifier NS_AVAILABL

区别在于之前的写法取出重用cell的时候可能是空的
而后来的写法如果取出空的那就自动创建一个新的 register就是告诉它创建个什么样的

### 提前计算好 cell 的高度和布局。
UITableView有两个重要的回调方法:

	- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath;

	- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath;

UITableView的回调顺序是先多次调用tableView:heightForRowAtIndexPath: 用来确定 contentSize 及  Cell 的位置，然后才会调用 tableView:cellForRowAtIndexPath:，从而来显示在当前屏幕的 cell 。
iOS8还会边滑动边调用 tableView:heightForRowAtIndexPath: ，可见这个方法里一定不能重复进行着大量的计算。我们应该提前计算好 cell 的高度并且缓存起来，在回调时直接把高度值直接返回。
这里说一种我经常采用的策略：
一般在网络请求结束后，在更新界面之前就把每个 cell 的高度算好，缓存到相对应的 model 中。

如果不提前做好高度计算，那么在app中看到的情况就是,一边下拉,整个cell不停得在刷新,在改变大小,这样用户体验不好。

所以在这个方法中最好不要进行大量的计算：

	- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath;

### 避免阻塞主线程。
很多时候我们需要从网络请求图片等，把这些操作放在后台执行，并且缓存起来。我们大都使用 SDWebImage 。

### 不要动态的add 或者 remove 子控件

最好在初始化时就添加完，然后通过hidden来控制是否显示。


### 使用第三方类库
现在facebook也推出了自己的类库[AsyncDisplayKit](https://github.com/facebook/AsyncDisplayKit)，可以达到每秒60帧的刷新。
不过使用下来觉得学习成本稍微大了一些。