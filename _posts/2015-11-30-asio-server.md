---
layout: post
title: 并发服务器搭建思路
category: 技术
---

####待解决问题：(已测试)

1. 以epoll为基础(后续blog有实例)， 设计类似boost::asio并发架构模型。
2. 以epoll为例， 如何在handle\_read()函数中，将收到数据添加到ctpl线程池之后，解决socket\_fd挂起问题。
因为异步，所以handle\_read返回后，此时还未修改fd由监听读改为监听写，所以是否仍然会从fd读取数据且再次调用handle\_read。(boost不会，安心)
3. 可以参考 [boost高并发网络框架+线程池](http://blog.chinaunix.net/uid-28163274-id-4984766.html "asio")、[Boost.Asio C++ 网络编程](https://mmoaay.gitbooks.io/boost-asio-cpp-network-programming-chinese/content/Chapter5.html "asio")及
*boost_1_59_0\libs\asio\example\cpp03\http\server2*

####设计思路

* 1、参考boost::asio example，子线程执行asyn_read、asyn_write后，回调asyn_read_callback、asyn_write_callback会在主线程执行。

* 2、使用std::deque分发recv到的数据到threadpool。

* 3、使用boost::threadpool或者CTPL线程池处理数据。每条数据加时间戳，超时(eg,.10s)直接关闭连接。

* 4、参考boost::asio example/cpp03/timeouts，优化超时时间。

* 5、使用libcds优化std::deque，使用Lock-free技术。[lock-free探究](http://lsclone.github.io/blog/%E6%8A%80%E6%9C%AF/2015/08/13/lock-free.html "lock-free")

* 6、消息队列deque进一步优化，除了添加时间戳，使用队列优先级和使用磁盘文件缓存队列部分数据。参考：[FIFO消息队列（内存、文件双缓冲模式）](http://blog.163.com/wwxwb_913/blog/static/97685362010851174237 "asio")

* 7、[协程](http://www.cppblog.com/jinq0123/archive/2016/05/20/213553.html "asio")



###初步实现(未考虑分发deque及时间戳)：

*boost\_1\_59\_0/libs/asio/example/cpp03/echo/async\_tcp\_echo\_server.cpp 添加如下代码：*

```
...
#include <ctpl.h>
ctpl::thread_pool tp(8 /* 8 threads in the pool */);
...

class session
{
...
private:
  void handle_read(const boost::system::error_code& error,
      size_t bytes_transferred)
  {
    if (!error)
    {
	  tp.push([this, bytes_transferred](int id){
		  // process task with this->data_
		  printf("recv data: %s\n", this->data_);

		  boost::asio::async_write(this->socket_,
			  boost::asio::buffer(this->data_, bytes_transferred),
			  boost::bind(&session::handle_write, this,
				boost::asio::placeholders::error));
	  });
    }
    else
    {
      delete this;
    }
  }
...
};

...

```
