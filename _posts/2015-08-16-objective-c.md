---
layout: post
title: Objective-C介绍及应用
category: 技术
---

Objective-C *参考网址：*

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

*参考网址：* 

* [Dictionaries: Collections of Keys and Values (官网)](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Collections/Articles/Dictionaries.html "oc")
* [NSDictionary和NSMutableDictionary例子微解](http://blog.csdn.net/like7xiaoben/article/details/7526730 "oc")

####3. objective-c数组的遍历方法

```
    Blog *blog1 = [[Blog blog] setBlogTitle:@"Love" andContent:@"I love you"];
    Blog *blog2 = [[Blog blog] setBlogTitle:@"Friendship" andContent:@"you are my best friend"];
    NSArray *array = [NSArray arrayWithObjects:@"hello",@"world",blog1,blog2, nil];
     
    //第一种遍历：普通for循环
    long int count = [array count];
    for (int i = 0 ; i < count; i++) {
        NSLog(@"1遍历array: %zi-->%@",i,[array objectAtIndex:i]);
    }
     
    //第二种遍历：快速for循环,需要有外变量i
    int i = 0;
    for (id obj in array) {
        NSLog(@"2遍历array：%zi-->%@",i,[array objectAtIndex:i]);
        i++;
    }
     
    //第三种遍历：OC自带方法enumerateObjectsUsingBlock:
     
    //默认为正序遍历
    [array enumerateObjectsUsingBlock:^(id obj, NSUInteger idx, BOOL *stop) {
        NSLog(@"3遍历array：%zi-->%@",idx,obj);
    }];
    //NSEnumerationReverse参数为倒序遍历
    [array enumerateObjectsWithOptions:NSEnumerationReverse usingBlock:^(id obj, NSUInteger idx, BOOL *stop) {
        NSLog(@"4倒序遍历array：%zi-->%@",idx,obj);
    }];
     
    //第四种遍历：利用枚举
    NSEnumerator *en = [array objectEnumerator];
    id obj;
    int j = 0 ;
    while (obj = [en nextObject]) {
        NSLog(@"5遍历array：%d-->%@",j,obj);
        j++;
    }
```

*参考网址：* [objective-c数组的四种遍历方法总结](http://my.oschina.net/pengloo53/blog/173349 "oc")

####4. block(块)

*参考网址：* [OC 的 Block 和 C++ 的 Lambda](http://www.jianshu.com/p/ccbd30af6283 "oc")

####5.protocol(协议)

**Protocol，简单来说就是一系列不属于任何类的方法列表，其中声明的方法可以被任何类实现。**
这种模式一般称为代理（delegation）模式。你通过Protocol定义各种行为，在不同的场景采用不同的实现方式。
在iOS和OS X开发中，Apple采用了大量的代理模式来实现MVC中View和Controller的**解耦**。

声明protocol:

```
@protocol ProcessDataDelegate <NSObject>
@required
- (void) processSuccessful: (BOOL)success;

@optional
- (id) submitOrder: (NSNumber *) orderid;
@end
```

实现protocol:

```
@interface TestAppDelegate : NSObject<ProcessDataDelegate>;
@end

@implementation TestAppDelegate
- (void) processSuccessful: (BOOL)success{
    if (success) {
        NSLog(@"成功");
    }else {
        NSLog(@"失败");
    }
}
@end 
```

*参考网址：*

* [Objective-C——消息、Category和Protocol](http://www.cnblogs.com/chijianqiang/archive/2012/06/22/objc-category-protocol.html "ios")
