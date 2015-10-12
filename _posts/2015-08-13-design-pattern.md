---
layout: post
title: C++设计模式
category: 技术
---

####后续工作陆续添加中...

1. [随笔分类 - C++设计模式](http://www.cnblogs.com/wanggary/category/294620.html "Markdown")
2. [C++设计模式培训](http://www.info-soft.cn/e/action/ShowInfo.php?classid=108&id=1080 "Markdown")

####一、 单件模式(Singleton)

资源回收 + 线程安全

*.h*

```
#include <memory>
#include <boost/thread/mutex.hpp>

class Singleton {
public:
	~Singleton() {}
	static Singleton* GetInstance();

protected:
	Singleton() {}

private:
	static std::auto_ptr<Singleton> pInstance;
	static boost::mutex mutex;
};
```

*.cpp*

```
std::auto_ptr<Singleton> Singleton::pInstance;

Singleton* Singleton::GetInstance() {
  if (!pInstance.get()) {
    boost::mutex::scoped_lock lock(mutex);
  	if (!pInstance.get()) {
  		pInstance = std::auto_ptr<Singleton>(new Singleton());
  	}
  }
  return pInstance.get();
}
```

*参考网址*：

* [C++中的单例模式](http://blog.csdn.net/hackbuteer1/article/details/7460019 "singleton")
* [C++单件模式实现](http://www.360doc.com/content/13/1021/17/12892305_323079174.shtml "singleton")

####二、

未完待续...
