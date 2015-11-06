---
layout: post
title: C++设计模式
category: 技术
---

####后续工作陆续添加中...

1. [随笔分类 - C++设计模式](http://www.cnblogs.com/wanggary/category/294620.html "Markdown")
2. [UML类图几种关系的总结](http://www.open-open.com/lib/view/open1328059700311.html "Markdown")
3. [C++设计模式培训](http://www.info-soft.cn/e/action/ShowInfo.php?classid=108&id=1080 "Markdown")

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

**C++11**版本单件模式实现：

```
Singleton& Singleton::getInstance() {
    static Singleton instance;
    return instance;
}
```

*参考网址*：

* [Double-Checked Locking is Fixed In C++11](http://preshing.com/20130930/double-checked-locking-is-fixed-in-cpp11/ "singleton")
* [How to implement multithread safe singleton in C++11 without using <mutex>](http://stackoverflow.com/questions/11711920/how-to-implement-multithread-safe-singleton-in-c11-without-using-mutex "singleton")

####二、接口实现

interface.h

```
#ifndef _INTERFACE_H_
#define _INTERFACE_H_

class Interface {
public:
  Interface() {}
  virtual ~Interface() {}

  virtual int Add(int, int) = 0;
  virtual double Div(int, int) = 0;

  static Interface* GetInstance();
};

#endif //_INTERFACE_H_
```

interface.cpp

```
#include "interface.h"
#include "caculator.h"

Interface* Interface::GetInstance() {
  return new Caculator();
}
```

caculator.h

```
#ifndef _CACULATOR_H_
#define _CACULATOR_H_

#include "interface.h"

class Caculator : public Interface {
public:
  Caculator() {}
  virtual int Add(int, int);
  virtual double Div(int, int);
};

#endif //_CACULATOR_H_
```

caculator.cpp

```
#include "caculator.h"

int Caculator::Add(int x, int y) {
  return (x + y);
}

double Caculator::Div(int x, int y) {
  return (x * 1.0 / y);
}
```

auto_pointer.h(智能指针)

```
#ifndef _AUTO_POINTER_H_
#define _AUTO_POINTER_H_

template <class ObjectType>
class wrap
{
public:
  wrap(ObjectType* pObject) : m_nRef(0),
    m_pObject(pObject) {}

  ~wrap() {
    delete m_pObject;
  }

  void addRef() {
    m_nRef++;
  }

  void subRef() {
    m_nRef--;
    if (0 == m_nRef) {
      delete this;
    }
  }

  ObjectType* ref() {
    return m_pObject;
  }

private:
  int m_nRef;
  ObjectType* m_pObject;
};

template <class ObjectType>
class auto_pointer {
public:
  static void create(auto_pointer<ObjectType>& origin,
    ObjectType* pObject) {
    if (NULL == pObject) {
      return;
    }
    origin.m_pObj = new wrap<ObjectType>(pObject);
    origin.m_pObj->addRef();
  }

  ~auto_pointer() {
    if (NULL != m_pObj) {
      m_pObj->subRef();
    }
  }

  auto_pointer() : m_pObj(NULL) {}

  auto_pointer(const auto_pointer<ObjectType>& origin) {
    if (NULL != origin.m_pObj) {
      m_pObj = origin.m_pObj;
      m_pObj->addRef();
    } else {
      m_pObj = NULL;
    }
  }

  const auto_pointer<ObjectType>& operator=
    (const auto_pointer<ObjectType>& origin) {
    if (NULL != origin.m_pObj) {
      m_pObj = origin.m_pObj;
      m_pObj->addRef();
    }
    return *this;
  }

  ObjectType* operator->() {
    if (NULL != m_pObj) {
      return m_pObj->ref();
    } else {
      throw NULL;
    }
  }

private:
  wrap<ObjectType>* m_pObj;
};

#endif //_AUTO_POINTER_H_
```

main.cpp

```
#include "stdio.h"
#include "interface.h"
#include "auto_pointer.h"

int main() {
  auto_pointer<Interface> p;
  auto_pointer<Interface>::create(p, Interface::GetInstance());
  try {
    printf("div : %.2f\n", p->Div(10, 3));
  } catch (...) {
    printf("smart_pointer should be initialized first.\n");
  }
  return 0;
}
```

优化如下：

interface.cpp

```
auto_pointer<Interface> Interface::GetInstance() {
  auto_pointer<Interface> p;
  auto_pointer<Interface>::create(p, new Caculator());
  return p;
}
```

main.cpp

```
#include "stdio.h"
#include "interface.h"
#include "auto_pointer.h"

void test(auto_pointer<Interface> p) {
  try {
    printf("sum : %d\n", p->Add(10, 3));
  } catch (...) {
  }
}

int main() {
	auto_pointer<Interface> p = Interface::GetInstance();
	try {
		printf("div : %.2f\n", p->Div(10, 4));
	} catch (...) {
		printf("smart_pointer should be initialized first.\n");
	}
	test(p);
	return 0;
}
```

####三、工厂模式

工厂模式分三类： 简单工厂模式、工厂方法模式、抽象工厂模式

![简单工厂模式](http://picture.jellythink.com/jellythinkSimplyFactoryDesign.png "简单工厂模式")

![工厂方法模式](http://picture.jellythink.com/jellythinkFactory%20Pattern.png "工厂方法模式")

![抽象工厂模式](http://picture.jellythink.com/jellythinkAbstractFactoryPattern.png "抽象工厂模式")

参考文档：

* [简单工厂模式](http://www.jellythink.com/archives/42 "factory")
* [工厂方法模式](http://www.jellythink.com/archives/62 "factory")
* [抽象工厂模式](http://www.jellythink.com/archives/75 "factory")

####四、命令模式

参考文档：[C++设计模式——命令模式](http://www.jellythink.com/archives/323 "command")

####五、State模式

![状态模式](http://picture.jellythink.com/jellythinkStatePattern.png "state")

参考文档：[C++设计模式——状态模式](http://www.jellythink.com/archives/382 "state")

####六、Observer模式

```
#include <iostream>
#include <list>
using namespace std;

class Observer
{
public:
     virtual void Update(int) = 0;
};

class Subject
{
public:
     virtual void Attach(Observer*) = 0;
     virtual void Detach(Observer*) = 0;
     virtual void Notify() = 0;
};

class ConcreteObserver : public Observer
{
public:
     ConcreteObserver(){}

     void Update()
     {
	cout<<"ConcreteObserver get the update."<<endl;
     }
};

class ConcreteObserver2 : public Observer
{
public:
     ConcreteObserver2(){}

     void Update()
     {
     	cout<<"ConcreteObserver2 get the update."<<endl;
     }
};

class ConcreteSubject : public Subject
{
public:
     void Attach(Observer* pObserver);
     void Detach(Observer* pObserver);
     void Notify();

private:
     std::list<Observer*> m_ObserverList;
};

void ConcreteSubject::Attach(Observer* pObserver)
{
     m_ObserverList.push_back(pObserver);
}

void ConcreteSubject::Detach(Observer* pObserver)
{
     m_ObserverList.remove(pObserver);
}

void ConcreteSubject::Notify()
{
     std::list<Observer*>::iterator it = m_ObserverList.begin();
     while (it != m_ObserverList.end())
     {
	(*it)->Update();
	++it;
     }
}
```

参考文档：[C++设计模式——观察者模式](http://www.jellythink.com/archives/359 "observer")

####七、MVC架构

参考文档：

* [MVC框架(百度)](http://baike.baidu.com/link?url=l3uoteKviMcV0wixD02JqqyyAE_dZryD3G0UaLGg3Qlowi-5ip7F0K1psMOKvGWEmqqC72USPYsbL81r58g_JK "mvc")
