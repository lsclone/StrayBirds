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

#### 4. Doing Work in Constructors

3. If the work **calls virtualfunctions,** these calls will not get dispatched to the subclass implementations.Future modification to your class can quietly introduce this problem even ifyour class is not currently subclassed, causing much confusion.

3. 如果在构造函数中**调用虚函数**，这些调用就会依赖于子类的实现。即使你的类还没有子类，未来对你的类的改动也会悄悄的引入这个问题而带来很多麻烦。

#### 5. Explicit Constructors 显式构造函数

Use the C++ keyword **explicit** for constructors with one argument.

在只有一个参数的构造前面加上**explicit**关键字。

Definition:

Normally, if a constructor takes one argument, it can be used as a conversion. For instance, if you define Foo::Foo(string name) and then pass a string to a function that expects a Foo, the constructor will be called to convert the string into a Foo and will pass the Foo to your function for you. This can be convenient but is also a source of trouble when things get converted and new objects created without you meaning them to. Declaring a constructor explicit prevents it from being invoked implicitly as a conversion.

定义：

一般来说，如果一个构造函数只有一个参数，这个参数是允许进行隐式转换的。例如，如果定义了一个Foo::Foo(string name)，然后向一个接受Foo类型的参数的函数传递一个string，这个构造函数就会被调用，将string对象构建为一个Foo对象，再将这个Foo对象传递给接受它的函数。这会方便我们使用，但也会引发一类你不需要的转换和创建新对象的问题。将一个构造函数声明为explicit可以避免隐式转换。

Decision:

We require all single argument constructors to be explicit. Always put explicit in front of one-argument constructors in the class definition: explicit Foo(string name);
The exception iscopy constructors, which, in the rare cases when we allow them, should probably not be explicit. Classes that are intended to be transparent wrappers around other classes are also exceptions. Such exceptions should be clearly marked with comments.

结论：

我们需要将所有的单参数的构造函数声明为explicit。但拷贝构造函数除外，这是少有的我们允许进行隐式转换的场合。用于对其它类进行透明包装的类也例外。这些例外应该用注释进行明确的说明。

#### 6. Copy Constructors 拷贝构造函数

Provide a copy constructor and assignment operator only when necessary. Otherwise, disable them with **DISALLOW_COPY_AND_ASSIGN**.

只有在必要时才提供拷贝构造函数和赋值运算符。否则，要用宏**DISALLOW_COPY_AND_ASSIGN**来禁止复制和赋值。

Decision:

Few classes need to be copyable. Most should have neither a copy constructor nor an assignment operator. In many situations, a pointer or reference will work just as well as a copied value, with better performance. For example, you can pass function parameters by reference or pointer instead of by value, and you can store pointers rather than objects in an STL container.

只有很少的类需要是可复制的。大多数类既不需要拷贝构造函数也不需要赋值运算符。在很多场合下，指针或引用可以像复制的值一样工作，性能还更好。例如，你可以用引用或者指针取代值来进行函数参数的传递，你也可以在STL容器中储存对象的指针而不是对象的值。

If your class needs to be copyable, prefer providing a copy method, such as CopyFrom() orClone(), rather than a copy constructor, because such methods cannot be invoked implicitly. If a copy method is in sufficient in your situation (e.g. for performance reasons, or because your class needs to be stored by value in an STL container), provide both a copy constructor and assignment operator.

如果你的类需要是可复制的，更好的方法是提供一个复制方法，比如CopyFrom()或Clone()，而不是提供拷贝构造函数，因为这些方法不可能被隐式调用。如果一个复制方法不能满足你的需求（比如因为性能原因，或者是你的类需要按值存放在STL容器中），那就同时提供拷贝构造函数和赋值运算符。

If your class does not need a copy constructor or assignment operator, you must explicitly disable them. To do so, add dummy declarations for the copy constructor and assignment operator in the private: section of your class, but do not provide any corresponding definition (so that any attempt to use them results in a link error).

如果你的类不需要拷贝构造函数或赋值运算符，你一定要显式的禁止它们。为了这么做，要将拷贝构造函数和赋值运算符声明为private且不提供任何的实现（这样试图调用它们就会产生链接时错误）。

For convenience, a **DISALLOW_COPY_AND_ASSIGN** macro can be used:

```
 // A macro to disallow the copy constructor and operator= functions
 // This should be used in the private:
 #define DISALLOW_COPY_AND_ASSIGN(TypeName) \
   TypeName(const TypeName&);               \
   voidoperator=(const TypeName&)
   
 Then, in classFoo:
 
 class Foo {
 public:
  Foo(int f);
  ~Foo();
 private:
  DISALLOW_COPY_AND_ASSIGN(Foo);
 };
```

#### 7. Multiple Inheritance 多重继承

Only very rarely is multiple implementation inheritance actually useful. We allow multiple inheritance only when at most one of the base classes has an implementation; all other base classes must be pure interface classes tagged with the Interface suffix.

多重继承只有在非常有限的场合比较有用。我们只允许有实现的基类不超过一个的多重继承；所有其它的基类都应该是用Interface后缀标记的纯接口类。

Note: There isan exception to this rule on Windows.

注意：Windows不符合这条准则。

#### 8. Interfaces 接口类

Classes that satisfy certain conditions are allowed, but not required, to end with an Interface suffix.

满足一些条件的类允许（但不必需）以Interface后缀结尾。

Definition: A class is a pure interface if it meets the following requirements:

1. It has only public pure virtual("= 0") methods and static methods (but see below for destructor).
2. It may not have non-static datamembers.
3. It need not have any constructors defined. If a constructor is provided, it must take no arguments and it must be protected.
4. If it is a subclass, it may only be derived from classes that satisfy these conditions and are tagged with the Interface suffix.

如果一个类满足下列要求，它就是一个纯接口：

1. 它只有公开的纯虚方法（“=0”）和静态方法（但看下面的析构函数一节）。
2. 它没有非静态的数据成员。
3. 它不需要定义任何构造函数。提供的构造函数必须是没有参数且是受保护的。
4. 如果它是一个子类，它只能从同样满足这些条件且有着Interface后缀标记的类中派生出来 。

An interface class can never be directly instantiated because of the pure virtual method(s) it declares. To make sure all implementations of the interface can be destroyed correctly, the interface must also declare a virtual destructor (in an exception to the first rule, this should not be pure). See Stroustrup, The C++ Programming Language, 3rd edition, section 12.4 for details.

一个接口类不能直接生成实例，因为其中声明的纯虚方法。为了保证这个接口的所有的实现都可以被直接销毁，这个接口必须声明一个虚的析构函数。

Pros:

Tagging a classwith the Interface suffix lets others know that they must not add implemented methods or non static data members. This is particularly important in the caseof multiple inheritance. Additionally, the interface concept is already well-understood by Java programmers.

优点：

用Interface后缀来标识一个类能让其它人知道他们禁止向其中添加方法的实现或非静态的数据成员。这在多重继承中尤其重要。另外，接口的概念已经被Java程序员所熟知了。

Cons:

The Interface suffix lengthens the class name, which can make it harder to read and understand. Also, the interface property may be considered an implementation detail that shouldn't be exposed to clients.

缺点：

Interface后缀增加了类名字的长度，这会令人难以阅读和理解。此外，接口特性作为实现细节不应该被暴露给客户端。

Decision:

A class may endwith Interface only if it meets the above requirements. We do not require the converse, however: classes that meet the above requirements are not required to end with Interface.

结论：

一个类只有在满足上述要求时才能用Interface后缀。但是我们不需要相反的规则：一个满足上述条件的类不以Interface结尾。
