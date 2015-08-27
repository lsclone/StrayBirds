---
layout: post
title: Android JNI 开发详解
category: 技术
---

*参考网址：* [Java Native Interface (JNI)](https://www3.ntu.edu.sg/home/ehchua/programming/java/JavaNativeInterface.html "Markdown")

####一、 JNI环境搭建: 

可以参考Android NDK开发详解进行如下操作：

* install jdk and set **JDK_HOME** win-system environment variable
* install cygwin & mingw for windows
* install eclipse

####二、 JNI简单实例：

* Step 1: Write a Java Class that uses C Codes - HelloJNI.java

```
public class HelloJNI {
   static {
      System.loadLibrary("hello"); // Load native library at runtime
                                   // hello.dll (Windows) or libhello.so (Unixes)
   }
 
   // Declare a native method sayHello() that receives nothing and returns void
   private native void sayHello();
 
   // Test Driver
   public static void main(String[] args) {
      new HelloJNI().sayHello();  // invoke the native method
   }
}
```

Compile the "HelloJNI.java" into "HelloJNI.class".

    > javac HelloJNI.java

* Step 2: Create the C/C++ Header file - HelloJNI.h


**相关网址**

[Java Native Interface Wiki](https://en.wikipedia.org/wiki/Java_Native_Interface#External_links "Markdown")
