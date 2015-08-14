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
