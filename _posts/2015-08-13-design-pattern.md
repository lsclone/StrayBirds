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

####三、工厂模式

未完待续...
