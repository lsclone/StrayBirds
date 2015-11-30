---
layout: post
title: 并发服务器搭建思路
category: 技术
---

* 1、参考boost::asio example，测试子线程执行asyn_read、asyn_write，是否会在主线程执行？(包括回调，degug跟踪)

* 2、使用std::list分发recv到的数据到threadpool

* 3、使用CTPL线程池处理数据

* 4、参考boost::asio example/cpp03/timeouts，优化超时时间。

* 5、使用libcds优化std::list，使用Lock-free技术。[lock-free探究](http://lsclone.github.io/blog/%E6%8A%80%E6%9C%AF/2015/08/13/lock-free.html "lock-free")
