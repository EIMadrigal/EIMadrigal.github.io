---
title: Seam Carving
url: seam-carving
date: 2020-07-24 13:36:00
description: 图像压缩算法
categories: Computer Science
tags: [Algorithm]
---

这是CS 61B的HW5，具体实现[在这里](https://github.com/EIMadrigal/CS61B/tree/master/hw5)。
## Intro
这个项目是要实现一种基于内容的图像缩放算法Seam Carving。Seam分为垂直（自上而下每行取一个像素点）和水平（自左向右每列取一个像素点）。  
下图是一张505*287的图像：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200707090755919.png)  
移除150条垂直seam，得到一张比原图窄30%的新图：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020070709085251.png)  
与传统的内容不可知的方法（裁剪、缩放）相比，seam curving可以保留原图的大多数重要特征。  
图像处理中的坐标表示与常见的笛卡尔坐标系不同：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200707094310172.png)  
每个像素的颜色采用RGB空间，与`java.awt.Color`一致。算法的过程分为3步：

 - Energy calculation  
因为是内容感知算法，所以需要一个指标衡量每个像素的**重要程度**，这个指标我们叫做该像素点的能量：能量越高越重要，就不太会被当做seam的一部分剔除。  
我们选择双梯度能量函数来计算能量。对于上面的冲浪图，计算后的灰度图如下：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200707095021383.png)  
可以看到：高能量像素对应颜色发生巨变的地方，比如冲浪者和大海的边界、天际线等，并且颜色更白。处理过程中就是要避免移除这些关键信息。
 - Seam identification  
计算出每个像素点的能量值后，就要找出一条能量值总和最小的seam，垂直seam从顶行的某像素点开始到最后一行某点结束。但是如果(x,y)位于seam，下一行只能选(x-1,y+1), (x,y+1),  (x+1,y+1)之一，可能是为了保证图像的连贯。
 - Seam Removal  
移除找到的seam。

## Implement
三个步骤的实现都在`SeamCarver`中，我们逐个来看：
 - 单像素能量计算  
采用对偶梯度能量函数$\Delta_x^2(x, y) + \Delta_y^2(x, y)$，x梯度的平方$\Delta_x^2(x, y) = R_x(x, y)^2 + G_x(x, y)^2 + B_x(x, y)^2$，$R_x(x, y), G_x(x, y), B_x(x, y)$是左右两个像素点(x+1, y)和(x-1, y)的红、绿、蓝之差的绝对值；类似地，对于y的梯度，就是要求上下两个像素点的差。对于边界的处理，采取循环方式，即如果某侧不存在，就取反方向的点。  
举例来看：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200707155708977.png)  
要计算(1, 0)位置即(255, 101, 153)的能量：  
$$R_x(1, 0) = 255 − 255 = 0, G_x(1, 0) = 101 − 101 = 0, B_x(1, 0) = 255 − 51 = 204$$  
故$\Delta_x^2(1, 0) = 204^2 = 41616$；对于y方向，由于没有(x, y-1)，就用(x, height-1)代替：  
$$R_y(1, 0) = 255 − 255 = 0, G_y(1, 0) = 255 − 153 = 102, B_y(1, 0) = 153 − 153 = 0$$  
故$\Delta_y^2(1, 0) = 102^2 = 10404$，所以(1, 0)位置的能量就是$41616 + 10404 = 52020$。  
接口也很简单`public  double energy(int x, int y)`。
 - Find Vertical Seam  
这个接口设计为`public int[] findVerticalSeam()`，返回的数组有H个值，第i个值对应要移除的第i行的列号。  
要找这样一条最短路径，我们考虑用动态规划求解：  
首先定义子问题$M(i,j)$表示以$(i,j)$结尾的最短路径的成本，用$e(i,j)$表示位置$(i,j)$的能量；  
接着寻找状态转移方程：由于路径的左右位置绝对值不大于1，所以$M(i,j)=e(i,j)+min\{M(i-1,j-1),M(i,j-1),M(i+1,j-1)\}$；  
最后确定base case：每行的值都由上一行确定，所以base case就是$M(i,0)=e(i,0)$。  
最终结果就是在最后一行找到$M$最小的像素点，逐行向上寻找三个相邻格子中$M$较小的那个。
 - Find Horizontal Seam  
对于水平方向的seam，当然也可以用动态规划求解。但是为了避免代码冗余，我们考虑利用`findVerticalSeam()`：先将图像转置，然后调用`findVerticalSeam()`，最后再将其转置即可。  
具体的：考虑如下3*2图像：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200708090038977.png)  
将其转置：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020070809031641.png)  
利用`findVerticalSeam()`得到(0,1,0)即为水平的seam，最后将图像再次转置即可。

## 待改进
 - 能量计算  
每次移除一条seam，都要调用`findVerticalSeam()`，`findVerticalSeam()`中会计算所有格子的能量，这样如果我们移除20条seam，就要计算20次所有格子的能量，显然这是可以避免的。  
最直观的方法就是空间换时间，创建能量矩阵`double[][]`存储每个格子的能量。
 - 水平seam  
转置矩阵耗时$O(WH)$，更快一些的做法是利用一个flag记录当前是在寻找垂直还是水平seam，在计算能量时判断分类。

## Reference
[HW 5: Seam Carving](https://sp18.datastructur.es/materials/hw/hw5/hw5)