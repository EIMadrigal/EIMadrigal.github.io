---
title: Image Filter and Recover
url: image-filter-and-recover
date: 2020-05-03 14:57:00
description: 图像处理和恢复
categories: Computer Science
tags: [Algorithm]
---

这是CS50的第四次大作业，顺便学习了图像的入门知识。
## 基础
黑白图(bitmap)的每个像素点只能取值0/1，1代表白色，0代表黑色。  
常见的图片格式有JPEG/PNG/BMP，这些格式都支持RGB，每个像素点可以用多个bit表示，常见的是24-bit，红、绿、蓝分别由8bit表示，范围0~255。  
BMP图的开始位置有两个header，第一个叫`BITMAPFILEHEADER`，14B；第二个叫`BITMAPINFOHEADER`，40B。接下来的每个像素点是按照BGR的顺序存储的。

## 过滤器
Image Filter就是对原图的像素点的像素进行操作，得到一幅新图。主要有下面几种：

 - Grayscale  
将RGB图变为灰度图。将每个像素点的R/G/B的值改为相同，值越大，亮度越大。一般取三色的平均值。
 - Sepia  
比较像怀旧滤镜，有很多算法可以做，主要就是对3种颜色乘一些系数，做一些加减运算。
 - Reflection  
左右翻转。
 - Blur  
图像模糊，对每个像素点的每种颜色，取其周围3*3格子的平均值。
 - Edges  
边缘检测，可以用Sobel Operator去做：  
Blur是对周围的格子取平均，Sobel是求一个加权和，对于x和y方向，有两个kernel：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200502165239931.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0VJTWFkcmlnYWw=,size_16,color_FFFFFF,t_70)  
对每个像素点的每种颜色，用周围3*3格子的对应颜色分别去乘Gx/Gy，得到加权和sumx/sumy。  
以x为例，如果左右两边差不多，那么加权和接近0，否则得到一个大正数/负数，说明很有可能是**两个物体的分界**。  
综合考虑x和y方向，取$\sqrt{sumx^2+sumy^2}$，再四舍五入到0~255之间。  
对于边缘的格子，可以做padding，围一圈全黑(0)的格子，相当于不用计算。

## 图片恢复
JPEG的前三个字节分别是`0xff, 0xd8, 0xff`，第四个字节的前四位是`1110`，这些可以唯一标识JPEG文件。  
记忆卡上所有图片是连续存储的，最小单位每块512B，不到一块的后面补0，不影响显示，每张图片可能占若干块。  
可以每次读512B扔到buffer里，如果是jpeg，就将其写入新文件、继续读512B，直到遇到下一个jpeg。