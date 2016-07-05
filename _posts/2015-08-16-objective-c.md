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
@interface TestAppDelegate : NSObject <ProcessDataDelegate>
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

####6. Category(分类)

Objective-C提供了一种与众不同的方式——Category，**可以动态的为已经存在的类添加新的方法**。这样可以保证类的原始设计规模较小，功能增加时再逐步扩展。使用Category对类进行扩展时，不需要访问其源代码，也不需要创建子类。Category使用简单的方式，实现了类的相关方法的模块化，把不同的类方法分配到不同的分类文件中。

原类：

```
SomeClass.h
@interface SomeClass : NSObject {
}
-(void) print;
@end 
```

分类SomeClass+Hello.h：

```
#import "SomeClass.h"

//category的名称: Hello
@interface SomeClass (Hello)
- (void)hello;
@end
```

分类SomeClass+Hello.m：

```
#import "SomeClass+Hello.h"

@implementation SomeClass (Hello)
- (void)hello {
    NSLog (@"name：%@ ", @"Jacky");
}
@end 
```

调用分类方法：

```
#import "SomeClass+Hello.h"
 
SomeClass *sc = [[SomeClass alloc] init];
[sc hello];
```

Category的使用场景：

1. 当你在定义类的时候，在某些情况下（例如需求变更），你可能想要为其中的某个或几个类中添加方法。
2. 一个类中包含了许多不同的方法需要实现，而这些方法需要不同团队的成员实现
3. 当你在使用基础类库中的类时，你可能希望这些类实现一些你需要的方法。

*参考网址：*

* [Objective-C——消息、Category和Protocol](http://www.cnblogs.com/chijianqiang/archive/2012/06/22/objc-category-protocol.html "ios")
* [iOS设计模式——Category](http://blog.csdn.net/lovefqing/article/details/8289851 "ios")
* [iOS在Xcode中怎么创建OC category文件](http://zhidao.baidu.com/link?url=AxLBD7cGt-g0dtTqvXPduf2a_gzxNUxpU-bSmX8E0hxd6YNLe7uPIgPb3oEOSQNCl3WekWmBdbzfOvgPSzSeWalEocgPO_jmhgavLhTfbS_ "ios")

####7. NSNotificationCenter & NSNotification (消息通知)

```
#define UPDATE_LGOGIN_INFO_NOTIFICATION @"updateLoginInfo"

@implementation KCMainViewController

/**
 *  添加监听
 */
-(void)addObserverToNotification{
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(updateLoginInfo:) name:UPDATE_LGOGIN_INFO_NOTIFICATION object:nil];
}

/**
 *  更新登录信息,注意在这里可以获得通知对象并且读取附加信息
 */
-(void)updateLoginInfo:(NSNotification *)notification{
    NSDictionary *userInfo=notification.userInfo;
    _lbLoginInfo.text=userInfo[@"loginInfo"];
    _btnLogin.titleLabel.text=@"注销";
}

@end
```

```
#define UPDATE_LGOGIN_INFO_NOTIFICATION @"updateLoginInfo"

@implementation KCLoginViewController

/**
 *  添加通知，注意这里设置了附加信息
 */
-(void)postNotification{
    NSDictionary *userInfo=@{@"loginInfo":[NSString stringWithFormat:@"Hello,%@!",_txtUserName.text]};
    NSLog(@"%@",userInfo);
    NSNotification *notification=[NSNotification notificationWithName:UPDATE_LGOGIN_INFO_NOTIFICATION object:self userInfo:userInfo];
    [[NSNotificationCenter defaultCenter] postNotification:notification];
//也可直接采用下面的方法
//    [[NSNotificationCenter defaultCenter] postNotificationName:UPDATE_LGOGIN_INFO_NOTIFICATION object:self userInfo:userInfo];

}

@end
```

*参考文档：* [iOS开发系列--通知与消息机制](http://www.cnblogs.com/kenshincui/p/4168532.html "ios")

####8. ios多线程之dispatch(asynchronous)

1. 获得主线程的dispatch队列，实际是一个串行队列: dispatch_get_main_queue();

2. 获得程序进程缺省产生的并发队列: dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
可设定优先级来选择高、中、低三个优先级队列。三个队列不代表三个线程，可能会有更多的线程。并发队列可以根据实际情况来自动产生合理的线程数。

```
/*
**  enum dispatch_queue_priority_t {
**      DISPATCH_QUEUE_PRIORITY_HIGH,
**      DISPATCH_QUEUE_PRIORITY_DEFAULT,
**      DISPATCH_QUEUE_PRIORITY_LOW,
**      DISPATCH_QUEUE_PRIORITY_BACKGROUND,
**  }
**  
 */

NSString* parameter = [self getSomeParameter];
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    NSString* result = [self fetchResultFromWebWithParameter:parameter];
    dispatch_async(dispatch_get_main_queue(), ^{
        [self updateUIWithResult:result];
    });
});
```

*参考网址：*

* [iOS开发之：dispatch_async 与 dispatch_get_global_queue 的使用方法](http://blog.sina.com.cn/s/blog_b22973cf01019ixl.html "ios")
* [iOS多线程的初步研究（八）-- dispatch队列](http://www.cnblogs.com/sunfrog/p/3305614.html "ios")
