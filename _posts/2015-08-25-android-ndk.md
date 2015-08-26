---
layout: post
title: Android NDK 开发详解
category: 技术
---

*参考网址：*[Native Development Kit (NDK)](https://www3.ntu.edu.sg/home/ehchua/programming/android/Android_NDK.html "Markdown")

####一、 Installing the Native Development Kit (NDK)

1. Setting up all the necessary tools for Android programming, such as JDK, Eclipse, Android SDK, Eclipse ADT (Read the 2nd method of [Android 5.0 开发环境搭建](http://lsclone.github.io/blog/%E6%8A%80%E6%9C%AF/2015/08/20/android5.html "Markdown")); and (for Windows Users) Cygwin (Read "How to install Cygwin" and "GCC and Make").

2. Download the Android NDK from http://developer.android.com/tools/sdk/ndk/index.html (e.g., android-ndk-r8-windows.zip).

3. Unzip the downloaded zip file into a directory of your choice (e.g., d:\myproject). The NDK will be unzipped as d:\myproject\android-ndk-r8. I shall denote the installed directory as <NDK_HOME>.

4. Include the NDK installed directory in the **PATH** environment variable.

####二、Writing a Hello-world Android NDK Program (NDK实例)

**Step 0: Read the Documentation**

Read "Android NDK" @ [http://developer.android.com/tools/sdk/ndk/index.html](Read "Android NDK" @ http://developer.android.com/tools/sdk/ndk/index.html "Markdown").

**Step 1: Write an Android JNI program**

Create an Android project called "AndroidHelloJNI", with application name "Hello JNI" and package "com.mytest". Create an activity called "JNIActivity" with Layout name "activity_jni" and Title "Hello JNI".

Replaced the "JNIActivity.java" as follows:

    package com.mytest;
     
    import android.os.Bundle;
    import android.app.Activity;
    import android.widget.TextView;
     
    public class JNIActivity extends Activity {
     
       static {
          System.loadLibrary("myjni"); // "myjni.dll" in Windows, "libmyjni.so" in Unixes
       }
     
       // A native method that returns a Java String to be displayed on the
       // TextView
       public native String getMessage();
     
       @Override
       public void onCreate(Bundle savedInstanceState) {
          super.onCreate(savedInstanceState);
          // Create a TextView.
          TextView textView = new TextView(this);
          // Retrieve the text from native method getMessage()
          textView.setText(getMessage());
          setContentView(textView);
       }
    }

**相关网址：**

1. [Windows环境下Android NDK环境搭建](http://blog.csdn.net/pengchua/article/details/7582949 "Markdown")

2. [How to install Cygwin](https://www3.ntu.edu.sg/home/ehchua/programming/howto/Cygwin_HowTo.html "Markdown")

3. [Cygwin官方下载页面](https://cygwin.com/install.html "Markdown")

4. [Android.mk | Android Developer -- google官网NDK之Android.mk(Makefile)说明文档](https://developer.android.com/ndk/guides/android_mk.html#mdv "Markdown") 

5. [Android NDK开发（2）----- JNI多线程](http://biancheng.dnbcw.info/shouji/388426.html "Markdown")
