---
layout: post
title: C++虚基类
category: 技术
---

*参考文档*：[虚基类(baidu)](http://baike.baidu.com/link?url=hEfQvUXY2R2HRcpCa-h7sdMxkKHPKEUnqwNT15AJaDrMPYcptPWgIWSGEOceGD2xIYS28dysWo90U5K18JBpGa "virtual inherit")

实例：

```
#include <stdio.h>

class A {
public:
	A(int aa) : aData(aa) {}
	virtual void a() { printf("%s\n", __FUNCTION__); }
	virtual void b() { printf("%s\n", __FUNCTION__); }
private:
	int aData;
};

class B : virtual public A {
public:
	B(int aa, int bb) : A(aa), bData(bb) {}
	virtual void a() { printf("%s\n", __FUNCTION__); }
	virtual void b() { printf("%s\n", __FUNCTION__); }
private:
	int bData;
};

class C : virtual public A {
public:
	C(int aa, int cc) : A(aa), cData(cc) {}
	virtual void a() { printf("%s\n", __FUNCTION__); }
	//virtual void b() { printf("%s\n", __FUNCTION__); }
private:
	int cData;
};

class D : public B, public C {
public:
	D(int aa, int bb, int cc, int dd) : A(aa), B(aa, bb), C(aa, cc), dData(dd) {}
	virtual void a() { printf("%s\n", __FUNCTION__); }
	//virtual void b() { printf("%s\n", __FUNCTION__); }
private:
	int dData;
};

void main() {
	D dd(1, 2, 3, 4);
	A* pA = &dd;
	pA->a();
	pA->b();
}
```
