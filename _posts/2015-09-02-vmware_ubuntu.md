---
layout: post
title: VMware + Ubuntu 安装介绍
category: 技术
---

**1. 安装VMware [VMware Workstation 12.0.0 Pro 正式版/注册码/注册机](http://www.52pojie.cn/forum.php?mod=viewthread&tid=405618&page=1 "VMware")**

**2. [在VMware虚拟机中使用安装并设置Ubuntu系统](http://jingyan.baidu.com/article/14bd256e0ca52ebb6d26129c.html "Ubuntu")**

需要注意以下几点：
> * [ubuntu官网下载页面](http://www.ubuntu.com/download/desktop "ubuntu")
> * 选择ubuntu64位，则需要在后续VMware安装时，Linux操作系统下选择Version: ubuntu-64bit
> * 新建虚拟机-->Guest Operating System Installation,  choose: **I will install the operating system later**
> * **Store virtual disk as a single file**
> * 可选操作： Virtual Machine Settings-->Processors, choose: **Virtualize Intel VT-x/EPT or AMD-V/RVI**. (**必须要在 BIOS 中启用了相关的 CPU 虚拟化功能，否则将开启不了虚拟机**)
> * Virtual Machine Settings-->CD/DVD(IDE), choose: **Use ISO image file**, 找到ubuntu-14.04.3-desktop-amd64.iso
> * 安装Ubuntu...

**3. 解决Ubuntu全屏问题：**

> Ubuntu -> 右上角 -> 系统设置 -> 硬件 -> 显示 -> 选择分辨率(eg.,1440x900(16:10)) -> 点击"**应用**"按钮

**4. 设置ubuntu的共享文件夹**

*参考*: [Win7与虚拟机VMware下运行的Ubuntu共享文件夹](http://blog.csdn.net/gaojinshan/article/details/9231853 "setting shared folder")

需要注意以下几点：
> * Install VMware Tools 之前需要先**弹出**Ubuntu 14.04.3 LTS amd64
> * 设置/更改密码：sudo passwd username (eg.,root)
> * 切换到root用户：su -> 回车 -> 输入密码；退出root用户: exit
> * 手动加载共享文件夹：mount -t vmhgfs .host:/ /mnt/hgfs
