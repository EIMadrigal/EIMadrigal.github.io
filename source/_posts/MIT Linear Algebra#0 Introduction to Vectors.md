---
title: MIT Linear Algebra 0 Introduction to Vectors
url: mit-linear-algebra-0
date: 2020-05-22 14:30:00
description: MIT 18.06
categories: Math
tags: [Linear Algebra]
---

#### *前言*
*线性代数应该是大部分工科和商科同学的必修课，然而很不幸的是：国内的线代教学简直一团糟。如同国内大学的其他课一样，一上来就是一堆不知所云的概念、定义和性质，然后是没有什么道理的计算技巧训练，期末考完试一切结束。如果你有时间看看MIT的18.06，相信绝对会刷新你对这个学科的认知，Gilbert Strang完美遵循了现实生活中遇到了什么问题、为什么会有这些问题、该如何解决、更好的方法这一教学链条。循循善诱、环环相扣，你会觉得上课、学习数学是一种享受。*

#### *方式*
*这门课我强烈建议去看Gil老爷子的视频，2020年的Lecture真心觉得不太良心，看完视频可以做做20版的作业加深理解。  
我会在Blog中专门记录这门课的笔记和理解，并且覆盖一些有趣的作业题。废话不多说，开始吧~*

---

## 笔记
课程的引入是通过初中的二元一次方程组：
$$
\begin{cases}
2x-y=0& \text{}\\
-x+2y=3& \text{}
\end{cases}$$
从几何上来看：就是二维平面上两条直线相交于$(1,2)$。  
从Row Picture来看：可以很直观地写作：
$$
\begin{bmatrix}
   2 & -1 \\
   -1 & 2
  \end{bmatrix}\begin{bmatrix}
   x \\
   y
  \end{bmatrix}=\begin{bmatrix}
   0 \\
   3
  \end{bmatrix}
$$
这种思考方式也是国内灌输的，第一行乘以第一列得到0，第二行乘以第一列得到3，但其实更重要的是从**向量(列)的线性组合**角度去考虑：
$$
x\begin{bmatrix}
   2 \\
   -1
  \end{bmatrix}+y\begin{bmatrix}
   -1 \\
   2
  \end{bmatrix}=\begin{bmatrix}
   0 \\
   3
  \end{bmatrix}
$$
这样从几何上解释就是：有两个向量$\begin{bmatrix}
   2 \\
   -1
  \end{bmatrix}$、$\begin{bmatrix}
   -1 \\
   2
  \end{bmatrix}$，要找到某个组合$(x,y)$可以得到向量$\begin{bmatrix}
   0 \\
   3
  \end{bmatrix}$。  
  类似地，三元一次方程组也可以从列向量线性组合的角度考虑，几何上扩展到三维空间。  
  由此推广到更加一般的情形：$Ax=b$，自然而然地，我们想知道：是否对于任意的$b$，此方程都有解？或者换句话：对于三元一次方程组，**列向量的线性组合是否能充满整个三维空间？**  
  如果三个向量共面，那么最多只能生成一个平面，也就是不能保证可以生成任意的$b$(有解)。后面会知道，有解的条件就是$A$**可逆**。  
  这节课最重要的一点就是要用**Column Picture去思考$Ax=b$**，为了加深理解，再举例：
  $$
\begin{bmatrix}
   2 & 5 \\
   1 & 3
  \end{bmatrix}\begin{bmatrix}
   1 \\
   2
  \end{bmatrix}=1*\begin{bmatrix}
   2 \\
   1
  \end{bmatrix}+2*\begin{bmatrix}
   5 \\
   3
  \end{bmatrix}=\begin{bmatrix}
   12 \\
   7
  \end{bmatrix}
$$

## 作业

 1. Draw two non-colinear vectors v and w, and the region that consists of all combinations cv+dw where 0 ≤ c ≤ 1  and 0 ≤ d ≤ 1.  Now consider the linear transformation of the unit square (all points (c,d) with  0 ≤ c ≤ 1 and  0 ≤ d ≤ 1)  by the 2x2 matrix with first column v and second column w.  Are these two regions the same?  
答：两个区域相同。
对$(c,d)$做线性变换，也即
$$
\begin{bmatrix}
   v&w \\
  \end{bmatrix}\begin{bmatrix}
   c \\
   d
  \end{bmatrix}=cv+dw(矩阵乘以列向量，即矩阵各列的线性组合)
$$
