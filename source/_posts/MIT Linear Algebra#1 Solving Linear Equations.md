---
title: MIT Linear Algebra#1 Solving Linear Equations
url: mit-linear-algebra-1
date: 2020-05-23 18:10:00
description: MIT 18.06
categories: Math
tags: [Linear Algebra]
---

## 矩阵消元
求解三元一次方程组$Ax=b$的方法就是**消元**：
$$
\begin{cases}
x+2y+z=2& \text{E1}\\
3x+8y+z=12& \text{E2}\\
4y+z=2& \text{E3}
\end{cases}$$
用$E2-3*E1$，再用$E3-2*E2$，增广矩阵的变化：
$$
\begin{bmatrix}
   A & b\\
  \end{bmatrix}=\begin{bmatrix}
   1 & 2 & 1 & 2\\
   3 & 8 & 1 & 12\\
   0 & 4 & 1 & 2
  \end{bmatrix}->\begin{bmatrix}
   1 & 2 & 1 & 2\\
   0 & 2 & -2 & 6\\
   0 & 4 & 1 & 2
  \end{bmatrix}->\begin{bmatrix}
   1 & 2 & 1 & 2\\
   0 & 2 & -2 & 6\\
   0 & 0 & 5 & -10
  \end{bmatrix}=\begin{bmatrix}
   U & c \\
  \end{bmatrix}$$
注意到变换过程中$A$的pivots(主对角线元素)均不为0。  
接着可以得到消元后的方程组：
$$
\begin{cases}
x+2y+z=2& \text{}\\
2y-2z=6& \text{}\\
5z=-10& \text{}
\end{cases}$$
从最后一个方程解起，并不断**回代**，就可以求得$(x,y,z)$的值。

如果回顾刚才的变换过程，并且用矩阵形式去表示：  
第一步：将$(2,1)$位置的值变0，即$E2-3*E1$：
$$
E_{21}A=\begin{bmatrix}
   1 & 0 & 0\\
   -3 & 1 & 0\\
   0 & 0 & 1
  \end{bmatrix}\begin{bmatrix}
   1 & 2 & 1\\
   3 & 8 & 1\\
   0 & 4 & 1
  \end{bmatrix}=\begin{bmatrix}
   1 & 2 & 1\\
   0 & 2 & -2\\
   0 & 4 & 1
  \end{bmatrix}
  $$
其实这个过程就是对单位阵做相同的行变换得到$E_{21}$，$E_{21}$的第一行乘以$A$本质上就是$A$的各行的线性组合：$1*row1+0*row2+0*row3=[1\ 2\ 1]$；同样的，$E_{21}$的第二行乘以$A$本质上还是$A$的各行的线性组合：$-3*row1+1*row2+0*row3=[0\ 2\ -2]$...  
第二步：将$(3,2)$位置的值变0，即$E3-2*E2$：
$$
E_{32}(E_{21}A)=\begin{bmatrix}
   1 & 0 & 0\\
   0 & 1 & 0\\
   0 & -2 & 1 
  \end{bmatrix}\begin{bmatrix}
   1 & 2 & 1\\
   0 & 2 & -2\\
   0 & 4 & 1
  \end{bmatrix}=\begin{bmatrix}
   1 & 2 & 1\\
   0 & 2 & -2\\
   0 & 0 & 5
  \end{bmatrix}
  $$
这个过程就是对单位阵做相同的行变换得到$E_{32}$，$E_{32}$的第三行乘以$(E_{21}A)$本质上就是$(E_{21}A)$的各行的线性组合：$0*row1+(-2*row2)+1*row3=[0\ 0\ 5]$；
所以整个变换过程用矩阵形式表示：  
$$E_{32}(E_{21}A)=U->(E_{32}E_{21})A=U(矩阵乘法结合律成立，交换律不成立)$$  
非常重要的结论就是**左行右列**：
$$
\begin{bmatrix}
   1 & 2 & 7\\
  \end{bmatrix}\begin{bmatrix}
  row1\\
  row2\\
  row3
  \end{bmatrix}=1*row1+2*row2+7*row3(矩阵左乘向量即行向量的线性组合)
  $$
$$
\begin{bmatrix}
   col1 & col2 & col3\\
  \end{bmatrix}\begin{bmatrix}
  3\\
  4\\
  5
  \end{bmatrix}=3*col1+4*col2+5*col3(矩阵右乘向量即列向量的线性组合)
  $$
**线性组合**的思想也是矩阵乘法的核心，再举一例：
$$
\begin{bmatrix}
   0 & 1\\
   1 & 0\\
  \end{bmatrix}\begin{bmatrix}
   a & b\\
   c & d\\
  \end{bmatrix}=\begin{bmatrix}
   c & d\\
   a & b\\
  \end{bmatrix}
  $$
  结果的第一行即：$0*[a\ b]+1*[c\ d]$，第二行即：$1*[a\ b]+0*[c\ d]$，交换行。  
  类似的，交换列(**列向量的线性组合**)：
  $$
\begin{bmatrix}
   a & b\\
   c & d\\
  \end{bmatrix}\begin{bmatrix}
   0 & 1\\
   1 & 0\\
  \end{bmatrix}=\begin{bmatrix}
   b & a\\
   d & c\\
  \end{bmatrix}
  $$

## 乘法和逆矩阵
回顾上一节的内容，对于$AB=C$：  
$C$的第$i$列是$A$的列向量的线性组合，组合系数即是$B$对应的列$col_i$，即：$A*col_i$；  
$C$的第$i$行是$B$的行向量的线性组合，组合系数即是$A$对应的行$row_i$，即：$row_i*B$。  
从这点出发，对于任意的矩阵乘法，都可以有：
$$AB=\Sigma(col_A*row_B)$$
举例来看：
  $$
\begin{bmatrix}
   2 & 7\\
   3 & 8\\
   4 & 9
  \end{bmatrix}\begin{bmatrix}
   1 & 6\\
   0 & 0\\
  \end{bmatrix}=\begin{bmatrix}
   2\\
   3\\
   4
  \end{bmatrix}\begin{bmatrix}
   1 & 6
  \end{bmatrix}+\begin{bmatrix}
   7\\
   8\\
   9
  \end{bmatrix}\begin{bmatrix}
   0 & 0
  \end{bmatrix}
  $$
再来看看$A=\begin{bmatrix}
   1 & 3\\
   2 & 6\\
  \end{bmatrix}$，能否找到一个**非零向量**$x$，使得$Ax=0$呢？  
答案是肯定的，因为$A$是不可逆的。  
解决不可逆这种特殊情况之前，先搞定足够好(可逆)的矩阵：
$$
AA^{-1}=\begin{bmatrix}
   1 & 3\\
   2 & 7\\
  \end{bmatrix}\begin{bmatrix}
   a & c\\
   b & d\\
  \end{bmatrix}=\begin{bmatrix}
   1 & 0\\
   0 & 1\\
  \end{bmatrix}=I
  $$
要求出$A^{-1}$，从列向量线性组合的角度：
$$
\begin{bmatrix}
   1 & 3\\
   2 & 7\\
  \end{bmatrix}\begin{bmatrix}
   a\\
   b\\
  \end{bmatrix}=\begin{bmatrix}
   1\\
   0\\
  \end{bmatrix}，\begin{bmatrix}
   1 & 3\\
   2 & 7\\
  \end{bmatrix}\begin{bmatrix}
   c\\
   d\\
  \end{bmatrix}=\begin{bmatrix}
   0\\
   1\\
  \end{bmatrix}
  $$
此时又回到了消元法解方程组，不过这里我们可以偷个懒，用Gauss-Jordan一次解出2个方程组：
$$
\begin{bmatrix}
   A & I\\
  \end{bmatrix}=\begin{bmatrix}
   1 & 3 & 1 & 0\\
   2 & 7 & 0 & 1\\
  \end{bmatrix}->\begin{bmatrix}
   1 & 0 & 7 & -3\\
   0 & 1 & -2 & 1\\
  \end{bmatrix}=\begin{bmatrix}
   I & A^{-1}\\
  \end{bmatrix}
  $$
再次回顾上一节的内容：消元过程中所作的变换都可以通过**左乘初等阵**实现，将变换过程中所有初等阵的乘积记作$E$，我们得到了一个激动人心的求解逆矩阵的方法：
$$
E\begin{bmatrix}
   A & I\\
  \end{bmatrix}=\begin{bmatrix}
   I & A^{-1}\\
  \end{bmatrix}
  $$
因为经过变换，$EA=I$，所以$E=A^{-1}$，比国内的伴随矩阵不知道好到哪里去了。

## A的LU分解
在了解为什么进行LU分解之前，我们先来看看高斯消元的时间复杂度：  
回想整个过程，如果矩阵$A$有$n$个元素，不难发现耗费的时间$n^2+(n-1)^2+...+1^2\approx \frac{n^3}{3}$，即$O(n^3)$；对于右侧的列向量$b(有m个元素)$，耗费$O(m^2)$。  
在很多时候，求解$Ax=b$时矩阵$A$是不变的，只有$b$在变化，如果每次都用消元法去解，每次的复杂度都会是$O(n^3)$，那么如果采用LU分解$A=LU$，只要预先准备好**下三角矩阵**$L$和**上三角矩阵**$U$，这一步复杂度$O(n^3)$，以后求解时：$Ax=LUx=b$，只要求解：

 - $Ly=b$，得到$y$，$O(n^2)$；
 - $Ux=y$，得到$x$，$O(n^2)$。  
以后每次求解只需要$O(n^2)$，大大提高了效率。

明白了原因后，我们看看具体的过程：  
我们知道，消元过程中矩阵$A$可以通过左乘矩阵$E$变为上三角矩阵$U$，即$EA=U$，那么$A=E^{-1}U=LU$。  
举例来看：  
如果$E_{32}E_{21}A=U$，那么$A=E_{21}^{-1}E_{32}^{-1}U=LU$，假设$E_{32}=\begin{bmatrix}
   1 & 0 & 0\\
   0 & 1 & 0\\
   0 & -5 & 1
  \end{bmatrix}$，$E_{21}=\begin{bmatrix}
   1 & 0 & 0\\
   -2 & 1 & 0\\
   0 & 0 & 1
  \end{bmatrix}$，那么求$L$即是求$E_{21}^{-1}和E_{32}^{-1}$，当然可以通过上一节中的拼单位阵来求解，但是对于初等阵，可以不用这么麻烦，以$E_{21}^{-1}$为例：  
这个变换是$row2-2*row1$，那么**逆矩阵就是要undo这个操作**，即$row2+2*row1$，所以$E_{21}^{-1}=\begin{bmatrix}
   1 & 0 & 0\\
   2 & 1 & 0\\
   0 & 0 & 1
  \end{bmatrix}$，同理可得$E_{32}^{-1}=\begin{bmatrix}
   1 & 0 & 0\\
   0 & 1 & 0\\
   0 & 5 & 1
  \end{bmatrix}$，按照**列线性组合**的思想，$L=E_{21}^{-1}E_{32}^{-1}=\begin{bmatrix}
   1 & 0 & 0\\
   2 & 1 & 0\\
   0 & 5 & 1
  \end{bmatrix}$。

## 置换
前面的消元过程中，当主元为0时，可能需要**交换行**来使消元继续下去，交换行的操作可以通过左乘置换矩阵实现，即$PA=LU$，前提是$A$可逆，否则再怎么交换，都会有零行。  
3阶矩阵的置换可以有$3!=6$种：
$$
\begin{bmatrix}
   1 & 0 & 0\\
   0 & 1 & 0\\
   0 & 0 & 1
  \end{bmatrix}、\begin{bmatrix}
   0 & 1 & 0\\
   1 & 0 & 0\\
   0 & 0 & 1
  \end{bmatrix}、\begin{bmatrix}
   0 & 0 & 1\\
   0 & 1 & 0\\
   1 & 0 & 0
  \end{bmatrix}、\begin{bmatrix}
   1 & 0 & 0\\
   0 & 0 & 1\\
   0 & 1 & 0
  \end{bmatrix}、\begin{bmatrix}
   0 & 1 & 0\\
   0 & 0 & 1\\
   1 & 0 & 0
  \end{bmatrix}、\begin{bmatrix}
   0 & 0 & 1\\
   1 & 0 & 0\\
   0 & 1 & 0
  \end{bmatrix}
$$
置换矩阵一个重要性质是：$P^T=P^{-1}$。

## 作业
Suppose $A = \begin{pmatrix} a & b \\ c & d \end{pmatrix}$ is factored into a 2x2 rotation $Q=\begin{pmatrix} \cos \theta & -\sin \theta \\ \sin \theta &\cos \theta \end{pmatrix}$ times a 2x2 lower triangular matrix $L=\begin{pmatrix} x & 0\\ y & z \end{pmatrix}$. Write $x,y,z$ and $θ$ in terms of $a,b,c$ and $d$.  
这道题不难，但容易漏解：  
容易得到：$\begin{pmatrix} x\cos{\theta}-y\sin{\theta} & -z\sin{\theta} \\ x\sin{\theta}+y\cos{\theta} & z\cos{\theta} \end{pmatrix} = \begin{pmatrix} a & b\\ c & d \end{pmatrix}$，故$b^2+d^2 = z^2$。对于旋转矩阵，$0\leq\theta\leq2\pi$。

 - $z = \sqrt{b^2+d^2}$：$\cos{\theta} = \frac{d}{\sqrt{b^2+d^2}},\sin{\theta} = -\frac{b}{\sqrt{b^2+d^2}}$
因为$L=Q^{-1}A$：
$$
\begin{pmatrix} \cos{\theta} & \sin{\theta} \\ -\sin{\theta} & \cos{\theta}\end{pmatrix}\begin{pmatrix} a & b\\c & d\end{pmatrix} = \begin{pmatrix}a\cos{\theta}+c\sin{\theta} & b\cos{\theta}+d\sin{\theta} \\ -a\sin{\theta}+c\cos{\theta} & -b\sin{\theta}+d\cos{\theta} \end{pmatrix} = \begin{pmatrix} x & 0 \\ y & z\end{pmatrix}
$$
所以有：
$$
x = \frac{ad - bc}{\sqrt{b^2+d^2}}, y = \frac{ab+cd}{\sqrt{b^2+d^2}}, \theta = \begin{cases}
\arccos\frac{d}{\sqrt{b^2+d^2}},b\le0\\
\\\arccos{\frac{d}{\sqrt{b^2+d^2}}}+\pi, b>0
\end{cases}
$$
$\arccos\theta$的值域是$[0,\pi]$。
 - $z = -\sqrt{b^2+d^2}$：$\cos{\theta} = -\frac{d}{\sqrt{b^2+d^2}},\sin{\theta} = \frac{b}{\sqrt{b^2+d^2}}$
$$
x = \frac{- ad + bc}{\sqrt{b^2+d^2}}, y = -\frac{ab+cd}{\sqrt{b^2+d^2}}, \theta = \begin{cases}
2\pi-\arccos\frac{d}{\sqrt{b^2+d^2}},b\le0\\
\pi-\arccos{\frac{d}{\sqrt{b^2+d^2}}}, b>0
\end{cases}
$$
