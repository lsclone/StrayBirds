---
layout: post
title: 并发服务器搭建思路
category: 技术
---

* 1、参考boost::asio example，子线程执行asyn_read、asyn_write后，回调asyn_read_callback、asyn_write_callback会在主线程执行。

* 2、使用std::list分发recv到的数据到threadpool。

* 3、使用CTPL线程池处理数据。每条数据加时间戳，超时(eg,.10s)直接关闭连接。

* 4、参考boost::asio example/cpp03/timeouts，优化超时时间。

* 5、使用libcds优化std::list，使用Lock-free技术。[lock-free探究](http://lsclone.github.io/blog/%E6%8A%80%E6%9C%AF/2015/08/13/lock-free.html "lock-free")

###初步实现(未考虑分发list及时间戳)：

*boost\_1\_59\_0/libs/asio/example/cpp03/echo/async\_tcp\_echo\_server.cpp 添加如下代码：*

```
...
#include <ctpl.h>
ctpl::thread_pool tp(10 /* two threads in the pool */);
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
