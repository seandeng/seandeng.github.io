---
title: UTC 时间
date: 2016-11-13 18:19:53
tags:
---

NSDate对象存放的日期始终是UTC的标准时间，可以根据这个时间进行其它时间的转换
所以debug去查看容易被误导 需要转换成string查看
<!--more-->

NSDateFormatter *dateFormatter = [[NSDateFormatter alloc] init];
[dateFormatter setDateFormat:@"yyyy-MM-dd'T'HH:mm:ssZ"];
NSDate *localDate = [dateFormatter dateFromString:@"2016-11-13T17:43:00+0000"];
NSLog(@"now Time = %@",[self getNowDateFromatAnDate:localDate]);

我现在在北京时间东八区 2016-11-13T17:43:00+0000 的字符串在 nsdate 类型里就是 2016-11-13 17:43:00 +0000 本地时间 2016-11-14 01:43:00 +0000
我现在在北京时间东八区 2016-11-13T17:43:00+0800 的字符串在 nsdate 类型里就是 2016-11-13 09:43:00 +0000 本地时间 2016-11-13 17:43:00 +0000

如果后面 是＋0800也就是和你所在时区一致的话，那么转换出来的本地时间时分秒也是一样的。

一些常用函数

	//NSString 2 NSDate
	- (NSDate *)stringToDate:(NSString *)strdate
	{
	    NSDateFormatter *dateFormatter = [[NSDateFormatteralloc] init];
	    [dateFormatter setDateFormat:@"yyyy-MM-dd HH:mm:ss"];
	    NSDate *retdate = [dateFormatter dateFromString:strdate];
	    [dateFormatter release];
	    return retdate;
	}

	//NSDate 2 NSString
	- (NSString *)dateToString:(NSDate *)date
	{
	    NSDateFormatter *dateFormatter = [[NSDateFormatteralloc] init];
	    [dateFormatter setDateFormat:@"yyyy-MM-dd HH:mm:ss"];
	    NSString *strDate = [dateFormatter stringFromDate:date];
	    [dateFormatter release];
	    return strDate;
	}

	//将本地日期字符串转为UTC日期字符串  
	//本地日期格式:2013-08-03 12:53:51  
	//可自行指定输入输出格式  
	-(NSString *)getUTCFormateLocalDate:(NSString *)localDate  
	{  
	    NSDateFormatter *dateFormatter = [[NSDateFormatter alloc] init];  
	    //输入格式  
	    [dateFormatter setDateFormat:@"yyyy-MM-dd HH:mm:ss"];  
	      
	    NSDate *dateFormatted = [dateFormatter dateFromString:localDate];  
	    NSTimeZone *timeZone = [NSTimeZone timeZoneWithName:@"UTC"];  
	    [dateFormatter setTimeZone:timeZone];  
	    //输出格式  
	    [dateFormatter setDateFormat:@"yyyy-MM-dd'T'HH:mm:ssZ"];  
	    NSString *dateString = [dateFormatter stringFromDate:dateFormatted];  
	    [dateFormatter release];  
	    return dateString;  
	}  
	  
	//将UTC日期字符串转为本地时间字符串  
	//输入的UTC日期格式2013-08-03T04:53:51+0000  
	-(NSString *)getLocalDateFormateUTCDate:(NSString *)utcDate  
	{  
	    NSDateFormatter *dateFormatter = [[NSDateFormatter alloc] init];  
	    //输入格式  
	    [dateFormatter setDateFormat:@"yyyy-MM-dd'T'HH:mm:ssZ"];  
	    NSTimeZone *localTimeZone = [NSTimeZone localTimeZone];  
	    [dateFormatter setTimeZone:localTimeZone];  
	      
	    NSDate *dateFormatted = [dateFormatter dateFromString:utcDate];  
	    //输出格式  
	    [dateFormatter setDateFormat:@"yyyy-MM-dd HH:mm:ss"];  
	    NSString *dateString = [dateFormatter stringFromDate:dateFormatted];  
	    [dateFormatter release];  
	    return dateString;  
	}  
