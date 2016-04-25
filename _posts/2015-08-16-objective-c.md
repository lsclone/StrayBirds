---
layout: post
title: Objective-C介绍及应用
category: 技术
---

Objective-C

*参考网址：*

* [Objective-C基础教程(入门教程)](http://wenku.baidu.com/link?url=S5_kWCMeaiI0SKW9ettcJ49ZCKjnozjJphiu3GSj26sSpZ0PzW5RqcLgYCyLVjnJ9jrq8qoM15iQyrMHplOlR1t9Ka-4Txkgr2iocYPj5CC "Objective-C")
* [Objective-C中的@property](http://www.devtalking.com/articles/you-should-to-know-property/ "Objective-C")
* [Objective-C中的Selector基本概念和操作](http://mobile.51cto.com/iphone-278572.htm "Objective-C")
* [Objective-C中不同方式实现锁(一)](http://www.tanhao.me/pieces/616.html/ "Objective-C")
* [iOS多线程的初步研究（二）-- 锁](http://www.cnblogs.com/sunfrog/p/3247361.html "Objective-C")

####1. NSString与int和float的相互转换

```
  NSString *tempA = @"123";
  NSString *tempB = @"456";

1，字符串拼接
 NSString *newString = [NSString stringWithFormat:@"%@%@",tempA,tempB];

2，字符转int
int intString = [newString intValue];

3，int转字符
NSString *stringInt = [NSString stringWithFormat:@"%d",intString];

4，字符转float
 float floatString = [newString floatValue];

5，float转字符
NSString *stringFloat = [NSString stringWithFormat:@"%f",intString];
```

####2. NSDictionary （Object-c中的key-value）

```
NSArray *array1 = [NSArray arrayWithObjects:@"iphone",@"ipod",nil];  
NSArray *array2 = [NSArray arrayWithObjects:@"mac",@"imac",@"mac pro",nil];  
////1、创建字典 ，类方法初始化自动释放  
NSDictionary *myDictionary = [NSDictionarydictionaryWithObjectsAndKeys:array1,@"mobile",array2,@"computers",nil];//注意用nil结束  
NSLog(@”myDictionary = %@”,myDictionary);  
//得到key-value的个数  
int dictSize = [myDictionary count];  
//根据key找到字典中的值  
NSArray *mobile = [myDictionary objectForKey:@"mobile"];  
//得到所有key  
NSArray *keys = [myDictionary allKeysForObject:array1];  
//得到所有value  
NSArray *values = [myDictionary allValues];  
//快速枚举  
for(id key in myDictionary)  
{  
    NSLog(@”key: %@,value: %@”,key,[myDictionary objectForKey:key]);  
}  
//如果字典只包含属性列表对象(NSData,NSDate,NSNumber,NSString,NSArray或NSDictionary)可以保存到文件中  
NSString *filePath = [[[NSBundlemainBundle]resourcePath]stringByAppendingPathComponent:@”dict.txt”];  
BOOL success = [myDictionary writeToFile:filePath atomically:YES];  
//用文件填充  
NSDictionary *myDict2 =[NSDictionary dictionaryWithContentsOfFile:filePath];  
  
//可变字典  
NSMutableDictionary *dictMutable = [[NSMutableDictionaryalloc]initWithObjectsAndKeys:array1,@”mobile”,array2,@”computer”, nil];  
NSString *string4 = @”stringTV”;  
//修改对象  
[dictMutable setObject:string4 forKey:@"media"];  
//删除对象  
[dictMutable removeObjectForKey:@"mobile"];  
//删除多个对象  
NSArray *keyArray =[NSArray arrayWithObjects:@"mobile",@"computer", nil];  
[dictMutable removeObjectForKey:keyArray];  
//删除所有对象  
  
[dictMutable removeAllObjects];  
```
