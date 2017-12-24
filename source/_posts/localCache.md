---
title: 使用 YTKKeyValueStore 做 iOS离线缓存
date: 2016-05-29 07:48:29
tags: 
  - YTKKeyValueStore
  - iOS离线缓存
---

## YTKKeyValueStore介绍

[YTKKeyValueStore](https://github.com/yuantiku/YTKKeyValueStore) 是iOS端的一个 Key-Value 存储类库。
封装了fmdb，使用Key-Value式的存储。在存储量不大的情况下，开发上的效率优势很大：

<!--more-->

1.Model层的代码编写简单，易于测试。
2.由于Value是JSON格式，所以在做Model字段更改时，易于扩展和兼容。


## 安装

在 Podfile 中加入下面一行代码来使用YTKKeyValueStore
	pod 'YTKKeyValueStore'
也可以手动添加源码 YTKKeyValueStore.h和YTKKeyValueStore.m 到你的工程中，并且在工程设置的Link Binary With Libraries中，增加libsqlite3.dylib

![](http://7xt1bu.com1.z0.glb.clouddn.com/14.jpeg)

## 使用

所有的接口都封装在YTKKeyValueStore类中。以下是一些常用方法说明。

### 打开（或创建）数据库

	// 打开名为test.db的数据库，如果该文件不存在，则创新一个新的。
	YTKKeyValueStore *store = [[YTKKeyValueStore alloc] initDBWithName:@"test.db"];

### 创建数据库表

	YTKKeyValueStore *store = [[YTKKeyValueStore alloc] initDBWithName:@"test.db"];
	NSString *tableName = @"user_table";
	// 创建名为user_table的表，如果已存在，则忽略该操作
	[store createTableWithName:tableName];

### 读写数据

YTKKeyValueStore类提供key-value的存储接口，存入的所有数据需要提供key以及其对应的value，读取的时候需要提供key来获得相应的value。

YTKKeyValueStore类支持的value类型包括：NSString, NSNumber, NSDictionary和NSArray，为此提供了以下接口：

	- (void)putString:(NSString *)string withId:(NSString *)stringId intoTable:(NSString *)tableName;
	- (void)putNumber:(NSNumber *)number withId:(NSString *)numberId intoTable:(NSString *)tableName;
	- (void)putObject:(id)object withId:(NSString *)objectId intoTable:(NSString *)tableName;

与此对应，有以下value为NSString, NSNumber, NSDictionary和NSArray的读取接口：

	- (NSString *)getStringById:(NSString *)stringId fromTable:(NSString *)tableName;
	- (NSNumber *)getNumberById:(NSString *)numberId fromTable:(NSString *)tableName;
	- (id)getObjectById:(NSString *)objectId fromTable:(NSString *)tableName;

### 删除数据接口

YTKKeyValueStore提供了以下接口用于删除数据。

	// 清除数据表中所有数据
	- (void)clearTable:(NSString *)tableName;

	// 删除指定key的数据
	- (void)deleteObjectById:(NSString *)objectId fromTable:(NSString *)tableName;

	// 批量删除一组key数组的数据
	- (void)deleteObjectsByIdArray:(NSArray *)objectIdArray fromTable:(NSString *)tableName;

	// 批量删除所有带指定前缀的数据
	- (void)deleteObjectsByIdPrefix:(NSString *)objectIdPrefix fromTable:(NSString *)tableName;

### 更多接口

YTKKeyValueStore 还提供了以下接口来获取表示内部存储的key-value对象。

	// 获得指定key的数据
	- (YTKKeyValueItem *)getYTKKeyValueItemById:(NSString *)objectId fromTable:(NSString *)tableName;
	// 获得所有数据
	- (NSArray *)getAllItemsFromTable:(NSString *)tableName;

## 在项目中的实际使用

在薄荷中 BHGlobalStore.h 封装了 YTKKeyValueStore

	@property (nonatomic, strong) YTKKeyValueStore *store;

	- (void)putGlobalObject:(id)object withId:(NSString *)objectId;
	- (id)globalObjectById:(NSString *)objectId;
	- (void)deleteGlobalObjectById:(NSString *)objectId;
	- (void)clearGlobalObjects;

BHGlobalStore.m 实现
    
初始化就创建了一个数据库 kDatabase 并且创建一个表 kGlobal，其它功能就是封装对这张表的操作，用来保证app中只有这一个数据库和表，方便管理。


	- (instancetype)init
	{
	    self = [super init];
	    if ( self ) {
	        self.store = [[YTKKeyValueStore alloc] initDBWithName:kDatabase];
	        [self.store createTableWithName:kGlobal];
	    }
	    return self;
	}

	- (void)putGlobalObject:(id)object withId:(NSString *)objectId
	{
	    [self.store putObject:object withId:objectId intoTable:kGlobal];
	}

	- (id)globalObjectById:(NSString *)objectId
	{
	    return [self.store getObjectById:objectId fromTable:kGlobal];
	}

	- (void)deleteGlobalObjectById:(NSString *)objectId
	{
	    [self.store deleteObjectById:objectId fromTable:kGlobal];
	}

	- (void)clearGlobalObjects
	{
	    [self.store clearTable:kGlobal];
	}

薄荷中使用Mantle处理Model层对象

	@interface BHActiveModel : MTLModel<MTLJSONSerializing>

BINGO_RESP_SPORTS_COURSES_COURSE_ID_SPORTS_DAYS 又继承了 BHActiveModel

### 存到表中

	-(void)saveToDB:(BINGO_RESP_SPORTS_COURSES_COURSE_ID_SPORTS_DAYS *)x{
		//将model转为json对象
	    NSDictionary *dic = [MTLJSONAdapter JSONDictionaryFromModel:x];
	    [app.store putGlobalObject:dic withId:PXSportsCourseRecordKey];
	}

### 从表中取出

	NSDictionary * dict = [app.store globalObjectById:PXSportsCourseForCourseId(self.courseId)];
	    if(dict){
	    	//将json转为model
	        BINGO_SPORTS_DAYS_DETAIL * cache =[BINGO_SPORTS_DAYS_DETAIL fromDictionary:dict];
	    ｝

## 总结

1.使用非常的方便 不用去管表的列 类型了,存取就一句代码。
3.因为存的是json，就算model改变或扩展，只需要改model其它业务代码都不需要更改。
4.适合数据量不大的存储。





