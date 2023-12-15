---
title: MIT Linear Algebra#5 Eigenvalues and Eigenvectors
url: mit-linear-algebra-5
date: 2020-05-31 20:07:00
description: MIT 18.06
categories: Math
tags: [Linear Algebra]
---

## 特征值/特征向量
矩阵作用于列向量$x$得到列向量$Ax$，矩阵的作用相当于函数，对于大部分列向量$Ax$，其方向是不同于$x$的，我们感兴趣的是其中**平行于**$x$的：$Ax=\lambda x,x\neq0$。即：向量$x$在矩阵$A$的作用下，方向不变，只进行比例系数为$\lambda$的伸缩。  
特征向量所在直线上的向量都是特征向量，并且包含了所有特征向量，组成了特征空间。如果我们不断左乘矩阵$A$，得到的列向量会越来越贴合最大特征值对应的特征空间（只对实数而言）。  
对于二阶投影矩阵$P$而言：如果$x$已经在列空间的平面上，那么$Px=x,\lambda=1$；如果$x$垂直于列空间的平面，则$Px=0,\lambda=0$，除此之外， 没有任何$x$可以在投影后与原$x$平行。  
若$A$是奇异阵，那么$Ax=0$必有非零解，所以$\lambda=0$必是一个特征值。  
特征值还有两条简单的性质：
$$\Sigma_{i=1}^{n}\lambda_i=trace(A),\lambda_1...\lambda_n=det(A)$$
有了理解后，求解$\lambda,x$也很自然：
$$(A-\lambda I)x=0有非零解，A-\lambda I必奇异$$
$$特征方程det(A-\lambda I)=0$$
解出$\lambda$，进而求出$(A-\lambda I)x=0$的零空间即可。  
举个交换阵的例子：$A=\begin{bmatrix}
   0 & 1 \\
   1 & 0\\
  \end{bmatrix}$，从物理意义上，交换$x_1=\begin{bmatrix}
   1\\
   1\\
  \end{bmatrix}$的两行仍然与原向量平行，此时$\lambda=1$；类似地，交换$x_2=\begin{bmatrix}
   1\\
   -1\\
  \end{bmatrix}$的两行仍然与原向量平行，只是变成了相反向量，此时$\lambda=-1$。  
  如果再看$A+3I=\begin{bmatrix}
   3 & 1 \\
   1 & 3\\
  \end{bmatrix}$，特征值变为了$\lambda+3=2,4$，特征向量没有改变。  
  接着可以看看特征值不为实数的例子：对于**反对称**矩阵$\begin{bmatrix}
   0 & -1 \\
   1 & 0\\
  \end{bmatrix}$，$\lambda=i,-i$，从几何上看：该矩阵的作用是将向量旋转90度，旋转之后的向量不可能与之前的平行，所以也就没有实数特征值。

## 对角化
这一节的前提是$A$**有$n$个线性无关的特征向量**，这样后面由**特征向量组成的矩阵**$S$才可逆。  
对于满足前提的矩阵：
$$AS=A\begin{bmatrix}
x_1&...& x_n \\
\end{bmatrix}=\begin{bmatrix}
\lambda_1x_1&...& \lambda_nx_n \\
\end{bmatrix}=\begin{bmatrix}
x_1&...& x_n \\
\end{bmatrix}\begin{bmatrix}
   \lambda_1 & ... & 0 \\
   ... & ... & ...\\
    0 & ... & \lambda_n \\
  \end{bmatrix}=S\Lambda$$
这样$S^{-1}AS=\Lambda$。  
如果$A$的所有特征值互异，必可对角化；如果有重复特征值，那么**不一定**有$n$个线性无关的特征向量，也不一定可以对角化。

$A$可以被分解为$A=S\Lambda S^{-1}$。由此不难得到$A$的幂：$A^K=S\Lambda^K S^{-1}$，特征值加倍，但特征向量不变。  
当$K\rightarrow+\infin$，如果所有$|\lambda_i|<1$，那么$A^K\rightarrow0$。  
$A$的幂有一个应用：一阶差分方程$u_{k+1}=Au_k$，通过递推不难发现$u_k=A^ku_0$，如果直接用$A^K=S\Lambda^K S^{-1}$求解，求逆开销是不可忽视的，所以我们换一种方式：  
我们知道，线性无关的特征向量可以作为基表示其它向量：
$$u_0=c_1x_1+...+c_nx_n=Sc,Au_0=S\Lambda S^{-1}u_0=S\Lambda S^{-1}Sc=S\Lambda c$$
$$A^ku_o=c_1\lambda_1^{k}x_1+...+c_n\lambda_n^{k}x_n=S\Lambda^{k}c$$
很清楚地看到：$u_k$的增长速度由$\Lambda$决定，并且越大的特征值起的作用越大。  
因此求解差分方程需要三步：

 1. 求解矩阵$A$的特征值和特征向量；
 2. 将$u_0$在特征向量上展开，求出向量$c$；
 3. 按照$u_k=S\Lambda^{k}c$计算即可。

这里非常经典的例子就是[斐波那契数列](https://www.cnblogs.com/EIMadrigal/p/11478906.html)。

## 微分方程
我们知道：对于常系数线性微分方程$\frac{dy}{dt}=\lambda y$，其解为$y(t)=Ce^{\lambda t}$。现在要研究的是未知函数是向量的情况：$\frac{du}{dt}=Au$，不难验证$u(t)=e^{\lambda t}x$是特解，并且微分方程组满足线性性质。  
举例来看：
$$
\begin{cases}
\frac{du_1}{dt}=-u_1+2u_2& \text{}\\
\frac{du_2}{dt}=u_1-2u_2& \text{}\\
\end{cases},u(0)=\begin{bmatrix}
   1\\
   0\\
  \end{bmatrix}
$$
$A=\begin{bmatrix}
   -1 & 2 \\
   1 & -2\\
  \end{bmatrix}$，求解出$\lambda=0,-3$，从特征值可以看出：$\lambda=-3$的项会随着$t$的增加而消失，$\lambda=0$的项最终会是稳态。
特征向量$x_1=\begin{bmatrix}
   2\\
   1\\
  \end{bmatrix},x_2=\begin{bmatrix}
   1\\
   -1\\
  \end{bmatrix}$，这样可以写出通解：
$$
u(t)=c_1e^{\lambda_1 t}x_1+c_2e^{\lambda_2 t}x_2=\frac{1}{3}\begin{bmatrix}
   2\\
   1\\
  \end{bmatrix}+\frac{1}{3}e^{-3t}\begin{bmatrix}
   1\\
   -1\\
  \end{bmatrix}
$$
当$t\rightarrow+\infin$，$\frac{1}{3}\begin{bmatrix}
   2\\
   1\\
  \end{bmatrix}$这一项将是稳态。
因此从特征值的角度，$||e^{(-3+6i)t}||=e^{-3t}$，$||e^{6it}||=1$，在单位圆上运动，所以最终的状态取决于特征值的实部：

 - $Re(\lambda)<0,e^{\lambda t}\rightarrow0,u(t)\rightarrow0$
 - 某个特征值为0，其余实部小于0，最终收敛于常量
 - $Re(\lambda)>0$，无法收敛

回头去看上述的微分方程，$u_1$和$u_2$耦合在一起，下面我们尝试用特征向量**解耦**：  
令$u=Sv$，则微分方程变为$S\frac{dv}{dt}=ASv,\frac{dv}{dt}=S^{-1}ASv=\Lambda v$，那么：
$$
\begin{cases}
\frac{dv_1}{dt}=\lambda_1v_1& \text{}\\
\frac{dv_2}{dt}=\lambda_2v_2& \text{}\\
...
\end{cases}
$$
换种思路，如果直接求解$\frac{dv}{dt}=\Lambda v$，那么类似于标量的答案$v(t)=v(0)e^{\Lambda t},u(t)=Sv(t)=Se^{\Lambda t}S^{-1}u(0)=e^{At}u(0)$，这里就得到了一个新的概念：**矩阵指数**$e^{At}$。  
如果你还记得高数里的泰勒展开：
$$
\frac{1}{1-x}=\sum\limits_{n=0}^{\infin}x^n,e^x=\sum\limits_{n=0}^{\infin}\frac{x^n}{n!}
$$
那么矩阵指数同样可以展开：
$$
(I-At)^{-1}=I+At+(At)^2+...,e^{At}=I+At+\frac{1}{2}(At)^2+...+\frac{(At)^n}{n!}+...
$$
$e^{At}$一定是收敛的，因为阶乘的增长速度远远大于其它运算，接着将它写成矩阵形式：
$$
e^{At}=I+S\Lambda S^{-1}t+\frac{1}{2}S\Lambda^2S^{-1}t^2+...=Se^{\Lambda t}S^{-1}
$$
$e^{\Lambda t}$也是一个矩阵指数，可以写作$\begin{bmatrix}
   e^{\lambda_1t} & ... & 0 \\
   ... & ... & ...\\
    0 & ... & e^{\lambda_nt} \\
  \end{bmatrix}$，这里也可以有相似的收敛性：

 - 对于矩阵指数$e^{\Lambda t}$，若$Re(\lambda)<0$，则收敛；
 - 对于矩阵幂$A^K=S\Lambda^K S^{-1}$，若$||\lambda||<1$，则收敛。

微分方程也可以像上一节一样，将二阶$y''+by'+ky=0$转为一阶，构造：
$$
\begin{cases}
y''+by'+ky=0& \text{}\\
y'=y'& \text{}\\
\end{cases}
$$
令$u=\begin{bmatrix}
   y'\\
   y\\
  \end{bmatrix}$，则$u'=\begin{bmatrix}
   y''\\
   y'\\
  \end{bmatrix}=\begin{bmatrix}
   -b & -k \\
   1 & 0\\
  \end{bmatrix}\begin{bmatrix}
   y'\\
   y\\
  \end{bmatrix}=Au$。

## 实对称阵/正定阵
**实对称矩阵的特征值必为实数，特征向量正交**。证明略。对于复矩阵，只有$A=\bar A^T(共轭转置)$，性质才成立。  
上一节我们知道：如果$A$有$n$个线性无关的特征向量，那么可以被分解成$A=S\Lambda S^{-1}$。对于正交阵而言$Q^T=Q^{-1}$，故$A=Q\Lambda Q^{-1}=Q\Lambda Q^{T}$，如果进一步计算：
$$
A=\begin{bmatrix}
q_1&...& q_n \\
\end{bmatrix}\begin{bmatrix}
   \lambda_1 & ... & 0 \\
   ... & ... & ...\\
    0 & ... & \lambda_n \\
  \end{bmatrix}\begin{bmatrix}
   q_1^T\\
   ...\\
   q_n^T\\
  \end{bmatrix}=\lambda_1q_1q_1^T+...+\lambda_nq_nq_n^T
$$
$q_iq_i^T$是投影矩阵，实对称矩阵可以由投影矩阵线性组合而来，这些投影矩阵我个人感觉非常像矩阵的基，也就是说实对称阵可以完全由其特征值和特征向量确定。

接着我们来看正定阵，**正定阵的前提是对称阵**，有3个充要条件：

 - $\lambda_i>0$
 - $pivot_i>0$
 - 所有子行列式为正

实际上，$\#正主元=\#正特征值$，并且$\Pi pivot=\Pi\lambda_i=det(A)$。  
利用正定阵可以研究二次型的最小值：  
$$f(x,y)=x^TAx=ax^2+2bxy+cy^2$$
如果$A$正定，那么$除(0,0)外,f(x,y)>0$。  
取$A=\begin{bmatrix}
   2 & 6 \\
   6 & 20\\
  \end{bmatrix}$，那么$f(x,y)=2x^2+12xy+20y^2$，配方$f(x,y)=2(x+3y)^2+2y^2>0$，注意各项的系数：两个平方项前的系数是$A$的两个**主元**，括号中的3是矩阵消元时所用的**乘数**。如果把$A$做LU分解会看得更清楚：$A=LU=\begin{bmatrix}
   1 & 0 \\
   3 & 1\\
  \end{bmatrix}\begin{bmatrix}
   2 & 6 \\
   0 & 2\\
  \end{bmatrix}$。  
从几何上看，$f(x,y)$就像是一个**碗**的形状，在$(0,0)$处取极小值0，$f(x,y)=1$则是椭圆截面。  
对于三阶的情况：$A=\begin{bmatrix}
   2 & -1 & 0 \\
  -1 & 2 & -1 \\
 0 & -1 & 2 \\
  \end{bmatrix}$，可以求得$\lambda=2-\sqrt2,2,2+\sqrt2$，那么此时
$$
f=x^TAx>0
$$
这在几何上已经上升到四维，必然有3个轴，并且轴的方向由相应的特征向量决定，轴的长度由特征值决定，$f=1$是一个椭球。

最后，如果$A_{mn}$的各列线性无关，那么$A^TA$必然正定，证明可以从$x^TAx>0$入手。

## 相似阵
前面我们见过$S^{-1}AS=\Lambda$，那么$A\sim\Lambda$。比较正式的说法是：存在可逆阵M，使得$B=M^{-1}AM$，则称$A\sim B$。相似阵可以看作一个家族，这个家族的共同点就是**特征值相同**。  
之前我们知道：如果$A$有$n$个不同的特征值，那么必可相似对角化。如果有重复的特征值，未必可以对角化：  
现在考虑$\lambda_1=\lambda_2=4$的情况，满足条件的矩阵有很多，比如$A=\begin{bmatrix}
   4 & 0 \\
   0 & 4\\
  \end{bmatrix}$，但如果我们去找$A$的相似阵，我们尝试用$M^{-1}AM=A$，无论任何$M$，最终的结果都是$A$自己，不会增加任何新的矩阵，矩阵$A$单独组成了一个家族。  
如果去看其余满足条件的矩阵，比如$B=\begin{bmatrix}
   4 & 1 \\
   0 & 4\\
  \end{bmatrix},C=\begin{bmatrix}
   4 & 0 \\
   17 & 4\\
  \end{bmatrix}...$，这些只有1个特征向量的矩阵虽然不能对角化，但是我们可以找一个**最接近对角阵**的，也就是$B$，称为Jordan Form。  
对于$\begin{bmatrix}
   0 & 1 & 0 & 0 \\
  0 & 0 & 1 & 0 \\
  0 & 0 & 0 & 0 \\
   0 & 0 & 0 & 0 \\
  \end{bmatrix}和\begin{bmatrix}
   0 & 1 & 0 & 0 \\
  0 & 0 & 0 & 0 \\
  0 & 0 & 0 & 1 \\
   0 & 0 & 0 & 0 \\
  \end{bmatrix}$，尽管特征值全为0，但并不相似。只有2个线性无关的特征向量，所以就有2个Jordan Block，每个Jordan Block长这样：
$$
J_i=\begin{bmatrix}
   \lambda_i & 1 &...& 0 \\
  0 & \lambda_i  & 1 & ...\\
  ...&...&...&...\\
  0 & 0 & \lambda_i  & 1 \\
   0 & 0 & 0 & \lambda_i  \\
  \end{bmatrix}
$$
每个块只能有1个特征向量，这样做的意义在于任意的矩阵$A$，即使不能相似对角化，但是都有$A\sim J=\begin{bmatrix}
   J_1 & ... & 0 \\
   ... & ... & ...\\
    0 & ... & J_d \\
  \end{bmatrix}$。

## SVD分解
假设我们在行空间有一组标准正交基$v_1,v_2,...,v_r$，左乘矩阵$A$进入列空间，将结果表示为列空间中的一组标准正交基$u_1,u_2,...,u_r$：
$$
AV=A\begin{bmatrix}
v_1&...& v_r \\
\end{bmatrix}=\begin{bmatrix}
u_1&...& u_r \\
\end{bmatrix}\begin{bmatrix}
   \sigma_1 & ... & 0 \\
   ... & ... & ...\\
    0 & ... & \sigma_r \\
  \end{bmatrix}=U\Sigma
$$
故$A$可以分解为$A=U\Sigma V^T$。
如果$A=\begin{bmatrix}
   4 & 4 \\
   -3 & 3\\
  \end{bmatrix}$，试着分解下，关键问题就是如何求得等式右边的3个矩阵。  
先来搞定$V$，最好能去掉$U$，我们的技巧是用$A^TA$：
$$
A^TA=V\Sigma^TU^TU\Sigma V^T=V\begin{bmatrix}
   \sigma_1^2 & ... & 0 \\
   ... & ... & ...\\
    0 & ... & \sigma_r^2 \\
  \end{bmatrix}V^T
$$
由于$A^TA$实对称，所以我们得到了$Q\Lambda Q^{T}$的形式，接下来只要搞定$A^TA$的特征值和特征向量即可得到$V$和$\Sigma$；  
同样地，为了求$U$，最好先搞掉$V$：
$$
AA^T=U\Sigma V^TV\Sigma^TU^T=U\begin{bmatrix}
   \sigma_1^2 & ... & 0 \\
   ... & ... & ...\\
    0 & ... & \sigma_r^2 \\
  \end{bmatrix}U^T
$$
只要求得$AA^T$的特征值和特征向量即可得$U$。

## 作业
Suppose we have the rank-r svd of a rank 1 matrix $A = U\Sigma V^T$. Describe the nullspace of $A$ in terms of possibly $U$, $Σ$, and $V$.  
Answer: The nullspace of $A$ is the same as the nullspace of $V^T$. Since $A$ is rank 1, $V$ is a vector. So the nullspace of $V^T$ is a hyperplane given by $V^Tx=0$, i.e., the space of all the vectors that are perpendicular to $V$.