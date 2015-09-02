---
layout: post
title: VMware + Ubuntu 安装介绍
category: 技术
---

* 1. 安装VMware [VMware Workstation 12.0.0 Pro 正式版/注册码/注册机](http://www.52pojie.cn/forum.php?mod=viewthread&tid=405618&page=1 "VMware")

* 2. [在VMware虚拟机中使用安装并设置Ubuntu系统](http://jingyan.baidu.com/article/14bd256e0ca52ebb6d26129c.html "Ubuntu")

需要注意以下几点：
> * [ubuntu官网下载页面](http://www.ubuntu.com/download/desktop "ubuntu")
> * 选择64位则需要在后续VMware安装时Linux操作系统下选择Version: ubuntu-64bit
> * 新建虚拟机-->Guest Operating System Installation,  choose: **I will install the operating system later**
> * **Store virtual disk as a single file**
> * Virtual Machine Settings-->Processors, choose: **Virtualize Intel VT-x/EPT or AMD-V/RVI**. (**必须要在 BIOS 中启用了相关的 CPU 虚拟化功能，否则将开启不了虚拟机**)
> * Virtual Machine Settings-->CD/DVD(IDE), choose: **Use ISO image file**, 找到ubuntu-14.04.3-desktop-amd64.iso
> * 安装Ubuntu...
