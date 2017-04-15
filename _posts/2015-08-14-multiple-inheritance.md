---
layout: post
title: C++虚基类
category: 技术
---

*参考文档*：[虚基类(baidu)](http://baike.baidu.com/link?url=hEfQvUXY2R2HRcpCa-h7sdMxkKHPKEUnqwNT15AJaDrMPYcptPWgIWSGEOceGD2xIYS28dysWo90U5K18JBpGa "virtual inherit")

实例：

```
#include <stdio.h>
#include <iostream>

using namespace std;

class A {
public:
	A(int aa) : aData(aa) {}
	virtual ~A() { cout << __FUNCTION__ << endl; }
	virtual void a() { printf("%s\n", __FUNCTION__); }
	virtual void b() { printf("%s\n", __FUNCTION__); }
private:
	int aData;
};

class B : virtual public A { // 虚基类
public:
	B(int aa, int bb) : A(aa), bData(bb) {}
	~B() { cout << __FUNCTION__ << endl; }
	virtual void a() { printf("%s\n", __FUNCTION__); }
	virtual void b() { printf("%s\n", __FUNCTION__); }
private:
	int bData;
};

class C : virtual public A { // 虚基类
public:
	C(int aa, int cc) : A(aa), cData(cc) {}
	~C() { cout << __FUNCTION__ << endl; }
	virtual void a() { printf("%s\n", __FUNCTION__); }
	//virtual void b() { printf("%s\n", __FUNCTION__); }
private:
	int cData;
};

class D : public B, public C {
public:
	D(int aa, int bb, int cc, int dd) : A(aa), B(aa, bb), C(aa, cc), dData(dd) {}
	~D() { cout << __FUNCTION__ << endl; }
	virtual void a() { printf("%s\n", __FUNCTION__); }
	//virtual void b() { printf("%s\n", __FUNCTION__); }
private:
	int dData;
};

int main() {
	A* pA = new D(1, 2, 3, 4);

	pA->a();
	pA->b();

	if (pA != nullptr)
		delete pA;

	return 0;
}
```
