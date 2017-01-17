---
layout: post
title: 图像处理部分算法
category: 技术
---

* [图像卷积与滤波的一些知识点](http://blog.csdn.net/zouxy09/article/details/49080029 "convolution & filter")
* [特征检测](https://zh.wikipedia.org/wiki/%E7%89%B9%E5%BE%81%E6%A3%80%E6%B5%8B "feature detection")

####1. 三角剖分算法

* [三角剖分算法(delaunay)](http://www.cnblogs.com/zhiyishou/p/4430017.html "triangulation")

####2. 图像缩放算法

*2.1 双线性差值算法*

* [最邻近差值和双线性差值算法](http://www.360doc.com/content/10/0907/17/474846_51910436.shtml "linear")
* [双线性插值](https://zh.wikipedia.org/wiki/%E5%8F%8C%E7%BA%BF%E6%80%A7%E6%8F%92%E5%80%BC "linear")
* [图像缩放--双线性插值算法](http://blog.csdn.net/xiaqunfeng123/article/details/17362881 "linear")

*2.2 三次样条差值算法*

* [三次样条插值(PDF)](http://netclass.csu.edu.cn/jpkc2008/China/09WebMaths/Teach_Method/Coursesppt/2/2.3%20%E4%B8%89%E6%AC%A1%E6%A0%B7%E6%9D%A1%E6%8F%92%E5%80%BC.pdf "spline")
* [三次样条插值和曲线拟合(代码实现)](http://haoxiang.org/2011/06/cubic-spline-interpolation-curve-fitting/ "spline")
* [OpenCV中resize函数五种插值算法的实现过程](http://blog.csdn.net/fengbingchun/article/details/17335477 "spline")

####3. 卷积

* [wiki-卷积](https://zh.wikipedia.org/wiki/%E5%8D%B7%E7%A7%AF "convolution")
* [OpenCV：卷积运算](http://blog.csdn.net/u010002704/article/details/43734731 "convolution")
* [图像处理基本算法-卷积和相关](http://www.cnblogs.com/libing64/archive/2011/12/13/2878738.html "convolution")
* [图像处理之理解卷积](http://blog.csdn.net/jia20003/article/details/7038938 "convolution")

####4. 高斯模糊（滤波）

要对数字图像做高斯模糊，就是用一个符合高斯函数分布的卷积核对数字图像做卷积运算。

* [图像处理--高斯滤波](http://blog.csdn.net/l_inyi/article/details/8915116 "Gaussian")
* [高斯函数以及在图像处理中的应用总结](http://blog.csdn.net/h_wlyfw/article/details/26456975 "Gaussian")

####5. 仿射变换(affine)

* [仿射变换](http://baike.baidu.com/link?url=SEM4nRPPywCzEhrNTave9MJotkH-xMmQW3jln5v3xYr-lS24CPx1FtGXbsrpJ06-jqyFovlco3bncYp-UwPEfeZSUaQNSiCj7-OHbUyOPk4oU7vyPt6oCmisBXwN9q87 "affine")

####6. 边缘检测算子

* [OpenCV图像处理篇之边缘检测算子](http://xiahouzuoxin.github.io/notes/html/OpenCV%E5%9B%BE%E5%83%8F%E5%A4%84%E7%90%86%E7%AF%87%E4%B9%8B%E8%BE%B9%E7%BC%98%E6%A3%80%E6%B5%8B%E7%AE%97%E5%AD%90.html "edge detect")

####7. 梯度

* [数字图像的梯度概念以及计算](http://www.xuebuyuan.com/2113139.html "gradient")

####8. 泰勒展开式

* [如何通俗地解释泰勒公式？](https://www.zhihu.com/question/21149770 "taylor")

####9. 矩阵及其微积分（求导）

* [矩阵求导](http://xuehy.github.io/2014/04/18/2014-04-18-matrixcalc/ "matrix")
* [矩阵、向量求导法则](http://www.cnblogs.com/huashiyiqike/p/3568922.html "matrix")
* [矩阵函数及其微积分](http://math.sjtu.edu.cn/course/Matrix/Chapter5.pdf "matrix")

####10. 图像金字塔

* [OpenCV图像金字塔](http://www.cnblogs.com/zsb517/archive/2012/06/10/2543739.html "pyramid")
* [OpenCV2.3.2图像金字塔](http://www.opencv.org.cn/opencvdoc/2.3.2/html/doc/tutorials/imgproc/pyramids/pyramids.html "pyramid")
* [OpenCV图像金字塔：高斯金字塔、拉普拉斯金字塔与图片尺寸缩放](http://blog.csdn.net/poem_qianmo/article/details/26157633 "pyramid")

####11. SIFT特征提取

* [SIFT特征提取分析](http://blog.csdn.net/abcjennifer/article/details/7639681 "sift")
* [SIFT算法原理解析](http://blog.csdn.net/CXP2205455256/article/details/41747325 "sift")
