---
layout: post
title: thrift异步IO服务器详解
category: 技术
---

转载：[Thrift异步IO服务器源码分析](http://yanyiwu.com/work/2014/12/06/thrift-tnonblockingserver-analysis.html "Markdown")

#### 任务的线程池

总所周知的是，异步服务器最适合的场景是高并发，IO 密集型程序。 对于 CPU 密集型的应用场景一般使用多线程服务来解决。
而对于 RPC 服务，TNonblockingServer 想使用异步 IO 来应对高并发。 
但是对于 rpc 远程函数调用，如果被方法的函数是 CPU 密集型的函数， 则运行该函数的过程整个主线程就会被阻塞，
也就是传说中的 【block the whole world】， 对于此，TNonblockingServer 的解决方法是将该函数包装成一个任务， 
然后扔进线程池，以此来避免主线程的阻塞。

线程池本身没什么好说的，但是在 TNonblockingServer 里 面需要了解的就是 线程池和主线程的交互：

当 TConnetion 的 app状态 进入 APP_READ_REQUEST 之后 读取完请求数据之后，则将任务包装好扔进线程池。
并且将状态改变(APP_READ_REQUEST -> APP_WAIT_TASK)：

```
// The application is now waiting on the task to finish
appState_ = APP_WAIT_TASK;
```

并且将该连接标识为 Idle ，如下函数：

```
// Set this connection idle so that [libevent] doesn't process more
// data on it while we're still waiting for the threadmanager to
// finish this task
setIdle();
```

setIdle 的目的在于将该连接对应的 socket事件标志位清空， 也就是在 Idle阶段不再关心该 socket是否有数据可读或者可写。

而当线程池里的某个 Task 运行完毕后，则会触发主线程的 pipe_event (上文中的已注册事件种的第二种事件)，告知主线程任务已完成。 如下：

```
// Signal completion back to the libevent thread via a pipe
if (!connection_->notifyIOThread()) {
  throw TException("TNonblockingServer::Task::run: failed write on notify pipe");
}
```

主线程收到通知之后，则会从 状态4(APP_WAIT_TASK) 转 移向 状态5(APP_SEND_RESULT) ，进入向 客户端发送函数调用结果的过程。

#### 总结

Thrift 的 TNonblockingServer 注释很丰富，原理清晰。 个人认为基本上是事件驱动服务器的入门教科书级代码了， 
事件驱动服务器核心在于状态转移， 因为事件驱动的原因，每次转换事件我们都需要保存当前的状态。 没啥，都是状态而已。

*后续工作：*

libevent封装了epoll。通过阅读thrift和libevent源码，详细分析libevent的事件循环(event loop) 机制、thrift的任务处理机制。
深入了解从epoll层层封装到thrift的并发服务器的**实现机制**。

##### 相关链接：

1. [Libevent源码分析](http://yanyiwu.com/work/2014/12/10/asyncronous-io-libevent.html "Markdown")
2. [Libevent官网](http://libevent.org/ "Markdown")
