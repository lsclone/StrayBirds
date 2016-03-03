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


>> 代码稍后上传
