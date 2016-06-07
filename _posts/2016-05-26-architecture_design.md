---
layout: post
title: 架构设计(随笔)
category: 技术
---

* 模拟IOS异步网络通信(NSOperationQueue)

```
@interface URLOperation : NSOperation {
  NSURLRequest* _request;
  NSURLConnection* _connection;
}
- (id)initWithURLString: (NSString*)url;
@property (readonly) NSData* data;
end

@implementation URLOperation

/*
**  work thread enter function
*/
- (void)start {
  if (![self isCancelled]) {
    _connection = [[NSURLConnection connectionWithRequest:_request deleget:self] retain];
    while(_connection != nil) {
      [[NSRunLoop currentRunLoop] runMode:NSDefaultRunLoopMode beforeDate:[NSDate distantFuture]];
    }
  }
}

#pragma mark NSURLConnection delegate Method
- (void)connecton: (NSURLConnection*)connection
  didReceiveData: (NSData*)data {
  [_data appendData:data];
}
@end
```

```
/*
**  asynchronous http request
**  threadpool will pull operation from queue to work thread
*/
- (IBAction)pushButton: (id)sender { // main thread
  NSOperationQueue* _queue = [[NSOperationQueue alloc] init];
  URLOperation* operation = [[URLOperation alloc] initWithURLString:@"http://google.com"];
  [_queue addOperation:operation];
}
```

后续分析：

接受服务器response数据后，回调以观察者(observer)模式添加到主线程消息处理队列实现。

1. [operation addObserver ...];
2. 将operation添加到main thread消息队列中
3. main thread遍历消息队列，执行[operation notify]
4. -(void)notify { loop: [observer update:_data]; }

