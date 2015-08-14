---
layout: post
title: google C++ 代码规范
category: 技术
---

#### Google C++ Style Guide

##### 1. Named Namespaces

```
// In the .h file
namespace mynamespace {

// All declarations are within the namespace scope.
// Notice the lack of indentation.
class MyClass {
 public:
  ...
  void Foo();
};

}  // namespace mynamespace
```

```
// In the .cc file
namespace mynamespace {

// Definition of functions is within scope of the namespace.
void MyClass::Foo() {
  ...
}

}  // namespace mynamespace
```

##### 2. Local Variables

```
vector<int> v;
v.push_back(1);  // Prefer initializing using brace initialization.
v.push_back(2);
```

```
vector<int> v = {1, 2};  // Good -- v starts initialized.
```

##### 3. Delegating and Inheriting Constructors

如果派生类没有数据成员需要初始化，且基类有多个构造函数，可以如下书写代码：

```
class Base {
 public:
  Base();
  Base(int n);
  Base(const string& s);
  ...
};

class Derived : public Base {
 public:
  using Base::Base;  // Base's constructors are redeclared here.
};
```

```
...
Derived d;
Derived d(10);
Derived d("hello");
...
```
