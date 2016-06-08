---
layout: post
title: boost后续添加工作
category: 技术
---

####1. boost::mutex

*参考网址*：

* [Boost 不同Mutex的大体说明](http://www.cppblog.com/ming81/archive/2012/07/18/184028.html "Mutex")
* [boost中的mutex与lock](http://blog.csdn.net/zp373860147/article/details/8186724 "Mutex")

####2. boost::asio

*参考网址*：

* boost\_1\_xx\_x/doc/html/boost_asio/overview.html
* [发布一个基于BOOST、ASIO的网络模型，特别适合小公司](http://bbs.chinaunix.net/thread-1493274-1-1.html "asio")
* [boost asio 应用方法学（二）——深入框架](http://blog.csdn.net/luansxx/article/details/7854326 "asio")
* [boost asio 应用方法学（三）——异步规范](http://blog.csdn.net/luansxx/article/details/7854329 "asio")

*参考代码库*：

* [Simple-Web-Server](https://github.com/lsclone/Simple-Web-Server "asio")
* [Simple-WebSocket-Server](https://github.com/lsclone/Simple-WebSocket-Server "asio")
* [websocketpp](https://github.com/lsclone/websocketpp "asio")

####3. boost线程池

*参考网址*：[boost之ThreadPool](http://blog.csdn.net/byxdaz/article/details/6299020 "threadpool")

note：

TIME_UTC编译会报错, 修改为boost::TIME_UTC_

####4. boost定时器

参考代码： [架构设计(随笔)-模拟IOS异步网络通信](http://lsclone.github.io/blog/%E6%8A%80%E6%9C%AF/2016/05/26/architecture_design.html "code")

*参考网址*：

* [boost.asio系列——Timer](http://www.cnblogs.com/TianFang/archive/2013/02/02/2890325.html "Markdown")
* [Boost.asio的简单使用(timer,thread,io_service类)](http://blog.163.com/miky_sun/blog/static/336940520104176206401/ "Markdown")

####5. boost智能指针

*参考网址*：

* [boost::scoped_ptr](http://blog.csdn.net/wuliming_sc/article/details/3820419 "Markdown")
* [boost::shared_ptr](http://blog.csdn.net/wuliming_sc/article/details/3820842 "Markdown")

####6. boost file system

遍历文件夹，代码待总结.

*参考网址*：[Boost Filesystem Library](http://www.ibm.com/developerworks/cn/aix/library/au-boostfs/ "Markdown")
