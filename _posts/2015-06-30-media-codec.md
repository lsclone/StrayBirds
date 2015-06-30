---
layout: post
title: 音视频编解相关工作
category: 技术
---

####工作如下：

参考链接： [最简单的基于FFMPEG的视频编码器（YUV编码为H.264）](http://blog.csdn.net/leixiaohua1020/article/details/25430425 "Markdown")

1. 恢复熟悉并提交ffmpeg相关测试用例代码
2. 恢复并熟悉图像格式yuv、rgb相关基础知识
3. 恢复并熟悉mpeg4、h.264、mp3等音视频压缩编码格式
4. 待解决问题：

   D:\work\demo\record_broadcast工程中用到了48字节的mpeg4 encoded data header

   是否完善为判断vos(video object segmentation)到

   第一个vop(video object plane/frame)之间的字节数，一般为55字节，需要做判断

   可以参考：http://blog.csdn.net/axdc_qa_team/article/details/4042762

   mpeg4 encoded data参考：

   QtDemo\enc_dec_ffmpeg\appmpeg4dec_zwj\tests\640X480.cmp

   QtDemo\enc_dec_ffmpeg\appmpeg4dec_zwj\tests\test_mpeg4_352X240.cmp

   6.2  查看移动硬盘QtDemo\enc_dec_ffmpeg\appmpeg4dec_zwj，查看是否dec的第一步为对vos的解码，

        以期获得解码vop后，得到的视频帧(图像)的各种参数：width，height，widthbytes，"framerate"

        如果上述猜测成功，那么对视频解码的理解就能解释通畅了。

   6.3  rgba(32 bits)有字节对齐问题，yuv420应该也有4字节对齐问题。

        参考ie收藏夹media codec "yuv格式_字节对齐"和"yuv数据格式"2个网页。

        参考例子： 摄像头记得应该是采集的yuv420格式的视频帧，是否有字节对齐。

        QtDemo\enc_dec_ffmpeg\appmpeg4dec和QtDemo\enc_dec_ffmpeg\appmpeg4enc是否有字节对齐例子

	x被n整除公式:

	( (x + (n-1)) >> log2(n) ) << log2(n)   -> ((x+3) >> 2) << 2

	(x + (n-1)) / n * n                     -> (x+3) / 4 * 4

        yuv420的widthbyte是否为： widthbyte = (width*3/2 + 3) / 4 * 4;

                             或:  widthbyte = ((width*3/2 + 3) >> 2) << 2;
