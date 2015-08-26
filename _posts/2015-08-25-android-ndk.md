---
layout: post
title: Android NDK 开发详解
category: 技术
---

*参考网址：*[Native Development Kit (NDK)](https://www3.ntu.edu.sg/home/ehchua/programming/android/Android_NDK.html "Markdown")

####一、 Installing the Native Development Kit (NDK)

1. Setting up all the necessary tools for Android programming, such as JDK, Eclipse, Android SDK, Eclipse ADT (Read **the 2nd method** of [Android 5.0 开发环境搭建](http://lsclone.github.io/blog/%E6%8A%80%E6%9C%AF/2015/08/20/android5.html "Markdown")); and (for Windows Users) Cygwin (Read "How to install Cygwin" and "GCC and Make").

2. Download the Android NDK from **http://developer.android.com/tools/sdk/ndk/index.html** (e.g., android-ndk-r8-windows.zip).

3. Unzip the downloaded zip file into a directory of your choice (e.g., d:\myproject). The NDK will be unzipped as d:\myproject\android-ndk-r8. I shall denote the installed directory as **NDK_HOME**.

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

**Step 2: Generating C/C++ Header File using "javah" Utility**

Create a folder "jni" under the project's root (right-click on the project ⇒ New ⇒ Folder). Create a sub-folder "include" under "jni" for storing the header files.

Run "javah" utility (**from a CMD shell**) to create C/C++ header called "HelloJNI.h":

```
> javah --help
......
// Change directory to <project-root>/jni/include
> javah -classpath ../../bin/classes;<ANDROID_SDK_HOME>\platforms\android-<xx>\android.jar 
  -o HelloJNI.h com.mytest.JNIActivity
```

参考实例(**cmd 命令行模式**)

    > javah -classpath ../../bin/classes;C:\adt-bundle-windows-x86_64-20140321\sdk\platforms\android-19\android.jar 
      -o HelloJNI.h com.mytest.JNIActivity

* -classpath: in our case, we need the JNIActivity.class which is kept in "<project-root>\bin\classes"; and its superclass Android.app.Activity.class which is kept in android.jar under the Android SDK.
* android.jar contains **android api classes** ([classes in android.jar](http://www.java2s.com/Code/Jar/a/Downloadandroidjar.htm "Markdown"))
* -o: to set the output filename.
* You need to use the fully-qualified name "com.mytest.JNIActivity".

**Step 3: C Implementation - HelloJNI.c**

Create the following C program called "HelloJNI.c" under the "jni" directory (right-click on the "jni" folder ⇒ New ⇒ File):

    #include <jni.h>
    #include "include/HelloJNI.h"
     
    JNIEXPORT jstring JNICALL Java_com_mytest_JNIActivity_getMessage
              (JNIEnv *env, jobject thisObj) {
       return (*env)->NewStringUTF(env, "Hello from native code!");
    }

**Step 4: Create an Android makefile - Android.mk**

Create an Android makefile called "Android.mk" under the "jni" directory (right-click on "jni" folder ⇒ New ⇒ File), as follows:

```
LOCAL_PATH := $(call my-dir)

include $(CLEAR_VARS)

LOCAL_MODULE    := myjni
LOCAL_SRC_FILES := HelloJNI.c

include $(BUILD_SHARED_LIBRARY)
```

In the above makefile, "myjni" is the name of our shared library (used in System.loadLibrary()), and "HelloJNI.c" is the source file.

**Step 5: Build NDK**

**Start a CMD shell**, change directory to the project's root directory, and run "ndk-build" script provided by Android NDK (the Android NDK installed directory shall be in the PATH).

```
// Change directory to <project-root>
> ndk-build
Compile thumb : myjni <= HelloJNI.c
SharedLibrary  : libmyjni.so
Install        : libmyjni.so => libs/armeabi/libmyjni.so
```

you can also build NDK with **cygwin** : 

```
// Change directory to <project-root>
> $NDK_HOME/ndk-build
```

NOTES:

* Use "ndk-build --help" to display the command-line options.
* Use "ndk-build V=1" to display the build messages.
* Use "ndk-build -B" to perform a force re-built.

**Step 6: Run the Android App**

Run the android app, via "Run As" ⇒ "Android Application". You shall see the message from the native program appears on the screen.

Check the "LogCat" panel to confirm that the shared library "libmyjni.so" is loaded.

    ...: Trying to load lib /data/data/com.example.androidhellojni/lib/libmyjni.so ...
    ...: Added shared lib /data/data/com.example.androidhellojni/lib/libmyjni.so ...

====================================================================

**MORE REFERENCES & RESOURCES**

1. [Windows环境下Android NDK环境搭建](http://blog.csdn.net/pengchua/article/details/7582949 "Markdown")

2. [How to install Cygwin](https://www3.ntu.edu.sg/home/ehchua/programming/howto/Cygwin_HowTo.html "Markdown")

3. [Cygwin官方下载页面](https://cygwin.com/install.html "Markdown")

4. [Android.mk | Android Developer -- google官网NDK之Android.mk(Makefile)说明文档](https://developer.android.com/ndk/guides/android_mk.html#mdv "Markdown") 

5. [Android NDK开发（2）----- JNI多线程](http://biancheng.dnbcw.info/shouji/388426.html "Markdown")
