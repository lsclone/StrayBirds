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

#### 4. DoingWork in Constructors

3. If the work **calls virtualfunctions,** these calls will not get dispatched to the subclass implementations.Future modification to your class can quietly introduce this problem even ifyour class is not currently subclassed, causing much confusion.

3. 如果在构造函数中**调用虚函数**，这些调用就会依赖于子类的实现。即使你的类还没有子类，未来对你的类的改动也会悄悄的引入这个问题而带来很多麻烦。

#### 5. Interfaces

Classes thatsatisfy certain conditions are allowed, but not required, to end with anInterface suffix.

满足一些条件的类允许（但不必需）以Interface后缀结尾。

Definition: A class is apure interface if it meets the following requirements:

1. It has only public pure virtual("= 0") methods and static methods (but see below for destructor).
2. It may not have non-static datamembers.
3. It need not have anyconstructors defined. If a constructor is provided, it must take no argumentsand it must be protected.
4. If it is a subclass, it mayonly be derived from classes that satisfy these conditions and are tagged withthe Interface suffix.

如果一个类满足下列要求，它就是一个纯接口：

1. 它只有公开的纯虚方法（“=0”）和静态方法（但看下面的析构函数一节）。
2. 它没有非静态的数据成员。
3. 它不需要定义任何构造函数。提供的构造函数必须是没有参数且是受保护的。
4. 如果它是一个子类，它只能从同样满足这些条件且有着Interface后缀标记的类中派生出来 。

An interfaceclass can never be directly instantiated because of the pure virtual method(s)it declares. To make sure all implementations of the interface can be destroyedcorrectly, the interface must also declare a virtual destructor (in anexception to the first rule, this should not be pure). See Stroustrup, The C++Programming Language, 3rd edition, section 12.4 for details.

一个接口类不能直接生成实例，因为其中声明的纯虚方法。为了保证这个接口的所有的实现都可以被直接销毁，这个接口必须声明一个虚的析构函数。

Pros:

Tagging a classwith the Interface suffix lets others know that they must not add implementedmethods or non static data members. This is particularly important in the caseof multiple inheritance. Additionally, the interface concept is alreadywell-understood by Java programmers.

优点：

用Interface后缀来标识一个类能让其它人知道他们禁止向其中添加方法的实现或非静态的数据成员。这在多重继承中尤其重要。另外，接口的概念已经被Java程序员所熟知了。

Cons:

The Interfacesuffix lengthens the class name, which can make it harder to read andunderstand. Also, the interface property may be considered an implementationdetail that shouldn't be exposed to clients.

缺点：

Interface后缀增加了类名字的长度，这会令人难以阅读和理解。此外，接口特性作为实现细节不应该被暴露给客户端。

Decision:

A class may endwith Interface only if it meets the above requirements. We do not require theconverse, however: classes that meet the above requirements are not required toend with Interface.

结论：

一个类只有在满足上述要求时才能用Interface后缀。但是我们不需要相反的规则：一个满足上述条件的类不以Interface结尾。
