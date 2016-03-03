---
layout: post
title: virtualbox + vagrant 虚拟系统搭建
category: 技术
---

* [how to config vm terminal using virtualbox & vagrant](https://github.com/trozet/sfc-random/blob/master/tacker_sfc_walkthrough.txt "markdown")
* [vagrant官网](https://www.vagrantup.com "markdown")

####安装vitualbox、vagrant 、配置虚拟系统环境

*参考文档：*

* [Windows7系统中使用vagrant构建Linux虚拟化开发环境](http://www.moqifei.com/archives/1270 "markdown")
* [在 Mac/win7 下上使用 Vagrant 打造本地开发环境](https://segmentfault.com/a/1190000002645737 "markdown")
* [vagrant 使用方法简介 virtualbox 的命令行管理工具 可以实现virtualbox后台运行](http://blog.csdn.net/samxx8/article/details/38941331 "markdown")
* [使用Vagrant在Windows下部署开发环境](https://blog.smdcn.net/article/1308.html "markdown")
* [Vagrant 知识澄清与杂症诊治](http://wushaobo.info/?p=83 "markdown")

####直接下载box失败解决方案

例如trozet/tacker_centos box正常下载流程：

```
>$ vagrant init trozet/tacker_centos
>$ vagrant up --provider virtualbox
```

由于国内网速不理想，可以分步执行：

1. 手动下载box：**https://atlas.hashicorp.com/trozet/boxes/tacker_centos/versions/1.1/providers/virtualbox.box**
2. vagrant box add base 本地的box文件名

*参考文档：*

* [trozet/tacker_centos box地址](https://atlas.hashicorp.com/trozet/boxes/tacker_centos "markdown")
* [a list of base boxes for vagrant](http://www.vagrantbox.es/ "markdown")
* [Box 下载慢的安装方法](http://mooc.guokr.com/discussion/7730/ "markdown")
