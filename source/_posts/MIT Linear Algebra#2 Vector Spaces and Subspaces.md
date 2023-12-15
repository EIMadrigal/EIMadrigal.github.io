---
title: MIT Linear Algebra#2 Vector Spaces and Subspaces
url: mit-linear-algebra-2
date: 2020-05-25 20:16:00
description: MIT 18.06
categories: Math
tags: [Linear Algebra]
---

## 向量空间
向量空间对于该空间内任意向量的线性组合(数乘/加法)都是封闭的，并且必然包含零向量(数乘0)。  
$R^2$本身就是一个向量空间，它的子空间有下面几种：

 - 过原点直线；
 - 零向量。

$R^3$本身也是一个向量空间，它的子空间：

 - 过(0,0,0)的平面；
 - 过(0,0,0)的直线；
 - 零向量。

从矩阵构造的角度来看，假设$A=\begin{bmatrix}
   1 & 3\\
   2 & 3\\
   4 & 1
  \end{bmatrix}$，$A$的每一列属于$R^3$，$A$的col1和col2的所有线性组合构成了一个向量空间，称作**列空间**，记作$C(A)$。  
从列空间的角度重新来看$Ax=b$：
$$
A=\begin{bmatrix}
   1 & 1 & 2\\
   2 & 1 & 3\\
   3 & 1 & 4\\
   4 & 1 & 5
  \end{bmatrix}
$$
$A$的所有列向量的线性组合构成了$R^4$的一个子空间，$Ax$恰是$A$的所有列向量的线性组合，即列空间$C(A)$，故只有$b$在$C(A)$中时方程组才有解。  
3列无论怎样线性组合，都无法充满整个4维空间，同时注意到$col1+col2=col3$，即使去掉第三列，仍然可以生成原来的列空间，$col3$与$col1$和$col2$是**线性相关**的，所以实际上矩阵$A$的列空间只是$R^4$中的2维子空间。

再来看看$Ax=0$，所有解$x$构成了$A$的**零空间**，记作$N(A)$。
$$
Ax=\begin{bmatrix}
   1 & 1 & 2\\
   2 & 1 & 3\\
   3 & 1 & 4\\
   4 & 1 & 5
  \end{bmatrix}\begin{bmatrix}
  x_1\\
  x_2\\
  x_3
  \end{bmatrix}=\begin{bmatrix}
  0\\
  0\\
  0\\
  0
  \end{bmatrix}
$$
虽然$A$的每一列都属于$R^4$，但是零空间研究的是$x$，$x$属于$R^3$。  
傻子都看得出来$(0,0,0)$是一组解，此前我们知道$col1+col2=col3$，所以$c(1,1,-1)$也是一组解，所有解其实就是$R^3$中的一个子空间，一条直线而已，也就是$A$的零空间$N(A)$。  
如果$Ax=b$中$b\neq0$，那么$x$是不能构成子空间的，因为其中没有零向量。

由此我们可以得到构造子空间的两种方法：

 - 矩阵各列的所有线性组合；
 - 方程组满足特定条件，让$x$生成子空间。

## 求解零空间
在上一节中我们知道，求解$A$的零空间其实就是求解$Ax=0$，还是要用到高斯消元。
$$
A=\begin{bmatrix}
   1 & 2 & 2 & 2\\
   2 & 4 & 6 & 8\\
   3 & 6 & 8 & 10
  \end{bmatrix}->\begin{bmatrix}
   1 & 2 & 2 & 2\\
   0 & 0 & 2 & 4\\
   0 & 0 & 0 & 0
  \end{bmatrix}=U
$$
很显然，主元(每一行中第一个非零元素)是$U(0,0)=1$和$U(2,3)=2$，pivot col是第一列和第三列，第二列和第四列是free col，也可知$rank(A)=\#pivots=2$，$\#自由变量=n-rank(A)$，于是写出化简后的方程组：
$$
\begin{cases}
x_1+2x_2+2x_3+2x_4=0& \text{}\\
2x_3+4x_4=0& \text{}
\end{cases}$$
对自由变量$x_2$和$x_4$，一般取$(0,1)$和$(1,0)$，所以特解(**零空间的一组基**)为：
$$
\begin{bmatrix}
  -2\\
  1\\
  0\\
  0
  \end{bmatrix}、\begin{bmatrix}
  2\\
  0\\
  -2\\
  1
  \end{bmatrix}$$
两个特解的线性组合即是整个零空间，也即是$Ax=0$的全部解：
$$
x=c\begin{bmatrix}
  -2\\
  1\\
  0\\
  0
  \end{bmatrix}+d\begin{bmatrix}
  2\\
  0\\
  -2\\
  1
  \end{bmatrix}$$
其实矩阵$U$还可以变得更加简单，可以化为简化行阶梯$R=\begin{bmatrix}
   1 & 2 & 0 & -2\\
   0 & 0 & 1 & 2\\
   0 & 0 & 0 & 0
  \end{bmatrix}$，即主元全部为1。  
仔细观察矩阵$R$，如果将pivot col全部移到左边，将free col移到右边，我们可以得到$R$的一般形式：$R=\begin{bmatrix}
   I & F\\
   0 & 0\\
  \end{bmatrix}$，由此得出$x$的一般形式：$x=\begin{bmatrix}
   -F\\
   I\\
  \end{bmatrix}$。

## 求解Ax=b
上一节中我们求解了$Ax=0$，接着看看更加复杂的情况：
$$
[A\ b]=\begin{bmatrix}
   1 & 2 & 2 & 2 & b_1\\
   2 & 4 & 6 & 8 & b_2\\
   3 & 6 & 8 & 10 & b_3
  \end{bmatrix}->\begin{bmatrix}
   1 & 2 & 2 & 2 & b_1\\
   0 & 0 & 2 & 4 & b_2-2b_1\\
   0 & 0 & 0 & 0 & b_3-b_2-b_1
  \end{bmatrix}
$$
我们知道，当$b$属于$C(A)$时方程组有解，不妨设$b=(1,5,6)$，那么化简的矩阵为$\begin{bmatrix}
   1 & 2 & 2 & 2 & 1\\
   0 & 0 & 2 & 4 & 3\\
   0 & 0 & 0 & 0 & 0
  \end{bmatrix}$，求解过程有3步：

 - 特解：一般令自由变量取0，即$x_2=x_4=0$，求主变量：
$$
\begin{cases}
x_1+2x_3=1& \text{}\\
2x_3=3& \text{}
\end{cases}$$
所以特解$x_p=\begin{bmatrix}
  -2\\
  0\\
  1.5\\
  0
  \end{bmatrix}$
 - 求零空间，即$Ax=0$的解$x_{null}$；
 - 所有解$x=x_p+x_{null}$。  
因为$Ax_p=b, Ax_{null}=0$，故$A(x_p+x_{null})=b$。

对于矩阵$A_{mn}$，我们知道$r(A)=\#pivots$，所以$r\leq m$，$r\leq n$。  
先看看**列满秩**的情况：每列都有主元，$r=n<m$，没有自由变量，零空间只有零向量：  
举例来看：
$$
\begin{bmatrix}
   1 & 3\\
   2 & 1\\
   6 & 1\\
   5 & 1
  \end{bmatrix}->\begin{bmatrix}
   1 & 0\\
   0 & 1\\
   0 & 0\\
   0 & 0
  \end{bmatrix}=\begin{bmatrix}
   I\\
   0\\
  \end{bmatrix}
$$
如果特解恰好存在，有1个解，否则无解。  
接着看看**行满秩**的情况：每行都有主元，$r=m<n$，**自由变量有$n-r=n-m$个**，零空间有$n-m$个基，$Ax=b$有无穷多解：  
举例来看：
$$
\begin{bmatrix}
   1 & 2 & 6 & 5\\
   3 & 1 & 1 & 1
  \end{bmatrix}->\begin{bmatrix}
   1 & 0 & a & b\\
   0 & 1 & c & d
  \end{bmatrix}=\begin{bmatrix}
   I & F\\
  \end{bmatrix}
$$
还有**满秩**的情况：$r=m=n$，没有自由变量，零空间只有零向量，必有唯一的特解：  
举例来看：
$$
\begin{bmatrix}
   1 & 2\\
   3 & 1\\
  \end{bmatrix}->\begin{bmatrix}
   1 & 0\\
   0 & 1\\
  \end{bmatrix}=I
$$
最后一种情况就是**不满秩**：$r<m$，$r<n$，$R=\begin{bmatrix}
   I & F\\
   0 & 0\\
  \end{bmatrix}$，如果特解存在，就有无穷多解；否则无解。

## 线性相关/基/维数
我们知道，对于矩阵$A_{mn}(m<n)$，因为有$n-r\geq n-m$个自由变量，将这些自由变量赋一些非零值，即可解得主元，所以$Ax=0$必有非零解。  
对于一组向量$x_1, x_2..., x_n$，除了系数全0以外，没有其他的线性组合可以得到零向量，那么这组向量**线性无关**，即$c_1x_1+c_2x_2+...+c_nx_n\neq0(c_i不全为0)$。  
举例来看：二维空间中的三个向量必然线性相关：
$$
A=\begin{bmatrix}
   2 & 1 & 3\\
   1 & 2 & -1\\
  \end{bmatrix}
$$
因为$n-r>0$，故必然有自由变量，所以$Ax=0$必有非零解，即线性相关。  
**基**也是一组向量$v_1, v_2..., v_d$，不过要满足2个条件：

 - 线性无关；
 - 可以生成整个空间。

**空间维度**即可以生成该空间的**基向量的个数**，前面我们知道：$r(A)=\#pivot\ cols$，所以$dim(C(A))=r(A)$，因为只需要pivot col就能生成整个列空间，并且列空间属于$R^m$，因为每个基向量都有$m$个元素。  
对于零空间来说，特解的个数就是自由变量的个数，也就是基向量的个数，即$dim(N(A))=\#自由变量=n-r(A)$，并且零空间属于$R^n$，因为每个解向量都有$n$个元素。

## 四个基本子空间
前面我们学习了列空间和零空间，很自然地，就会有行空间和$A^T$的零空间：  
行空间，顾名思义，即是矩阵行向量的所有线性组合生成的向量空间，其实就是$C(A^T)$，$dim(C(A^T))=r(A)$，属于$R^n$；  
$A^T$的零空间，即$A^Tx=0$的所有解向量生成的向量空间，即$N(A^T)$，$dim(N(A^T))=m-r(A)$，属于$R^m$。  
回忆消元的过程，我们不停地进行初等行变换，这个过程中，行空间没有改变，列空间改变，最终的行空间就是$R$矩阵的前$r(A)$行生成的向量空间。  
对于$N(A^T)$，即$A^Ty=0$，转置即有$y^TA=0$，所以$N(A^T)$又叫**左零空间**。  
学习消元时我们知道，左乘一系列的初等阵可以将$A$化为$R$：
$$
E\begin{bmatrix}
   A_{mn} & I_{mm}\\
  \end{bmatrix}->\begin{bmatrix}
   R_{mn} & E_{mm}\\
  \end{bmatrix}
$$
矩阵$E$记录了我们的变换过程，举例来看：
$$
EA=\begin{bmatrix}
   -1 & 2 & 0\\
   1 & -1 & 0\\
   -1 & 0 & 1
  \end{bmatrix}\begin{bmatrix}
   1 & 2 & 3 & 1\\
   1 & 1 & 2 & 1\\
   1 & 2 & 3 & 1
  \end{bmatrix}->\begin{bmatrix}
   1 & 0 & 1 & 1\\
   0 & 1 & 1 & 1\\
   0 &  0& 0 & 0
  \end{bmatrix}=R
$$
$dim(N(A^T))=m-r(A)=3-2=1$，左零空间中唯一一个基向量即$E$的最后一行，因为$\begin{bmatrix}
   -1 & 0 & 1
  \end{bmatrix}A=0$。

## 矩阵空间
前面我们研究了若干向量生成的空间，上升一个高度，若干矩阵也可以构成一种特殊的向量空间，即矩阵空间。  
所有的三阶矩阵构成矩阵空间$M$，也就是$R^{3*3}$。  
$M$的子空间有上三角矩阵$U$和对称矩阵$S$(可以用封闭性验证)。明确了空间后，就要研究该空间的维数和基向量。  
$dim(M)=9$，因为需要9个矩阵构成一组基，而且我们可以写出一组基：
$$
\begin{bmatrix}
   1 & 0 & 0\\
   0 & 0 & 0\\
   0 & 0 & 0
  \end{bmatrix}、\begin{bmatrix}
   0 & 1 & 0\\
   0 & 0 & 0\\
   0 & 0 & 0
  \end{bmatrix}、\begin{bmatrix}
   0 & 0 & 1\\
   0 & 0 & 0\\
   0 & 0 & 0
  \end{bmatrix}
  ...\begin{bmatrix}
   0 & 0 & 0\\
   0 & 0 & 0\\
   0 & 0 & 1
  \end{bmatrix}
$$
$dim(S)=6$，因为需要对角线的3个元素和对角线下面(上面)3个元素；  
$dim(U)=6$，因为需要对角线的3个元素和对角线上面3个元素。  
再来看看$S\bigcap U$，既是上三角矩阵又是对称矩阵，其实就是对角阵，$dim(S\bigcap U)=3$；  
那么$S\bigcup U$呢？属于上三角或者对称，很显然这无法构成子空间；  
那么$S+U$呢？对应元素求和，实际上这就是$M$。  
由此我们得到一个性质：
$$
dim(S)+dim(U)=dim(S\bigcap U)+dim(S+U)
$$
对于向量空间和基，不应局限于线性代数中，例如熟悉的微分方程：
$$
\frac{d^2y}{dx^2}+y=0
$$
它的所有解$y=c_1cosx+c_2sinx$也构成零空间，那么$cosx$和$sinx$就是一组基，并且解空间的维数是2。

最后看一种很有趣的矩阵，秩为1的矩阵：  
对于这种矩阵，有$dim(C(A))=dim(C(A^T))=r=1$，不妨举个例子：
$$
A=\begin{bmatrix}
   1 & 4 & 5\\
   2 & 8 & 10
  \end{bmatrix}=\begin{bmatrix}
   1\\
   2\\
  \end{bmatrix}\begin{bmatrix}
   1 & 4 & 5\\
  \end{bmatrix}=uv^T
$$
用一个例子作为结尾：
在$R^4$中，$v=\begin{bmatrix}
   v_1\\
   v_2\\
   v_3\\
   v_4
  \end{bmatrix}$，$s$是满足$v_1+v_2+v_3+v_4=0$的所有$v$，那么$s$显然是子空间，并且可以写成矩阵形式$Av=\begin{bmatrix}
   1 & 1 & 1 & 1\\
  \end{bmatrix}v=0$，接着看看$A$的四个基本子空间：

 - 零空间：$dim(N(A))=n-r=4-1=3$，给每个自由变量赋值后，得到一组特解(基)：
$$
\begin{bmatrix}
   -1\\
   1\\
   0\\
   0
  \end{bmatrix}、\begin{bmatrix}
   -1\\
   0\\
   1\\
   0
  \end{bmatrix}、\begin{bmatrix}
   -1\\
   0\\
   0\\
   1
  \end{bmatrix}
$$
 - 列空间：$dim(C(A))=r=1$，基可以取任意一列；
 - 行空间：$dim(C(A^T))=r=1$，基向量即第一行；
 - 左零空间：$dim(N(A^T))=m-r=0$，基向量只有零向量。

## 作业
Under what possible conditions is the matrix $A=uv^T+wz^T$ not of rank 2?  
对于$uv^T=\begin{bmatrix}
   a\\
   b\\
   c\\
  \end{bmatrix}\begin{bmatrix}
   v_1&v_2&v_3\\
  \end{bmatrix}$，结果就是$u$的线性组合$\begin{bmatrix}
   v_1u&v_2u&v_3u\\
  \end{bmatrix}$，所以$C(uv^T)\subset C(u)=xu,x\in R$；同样地，$C(wz^T)\subset C(w)=yw,y\in R$，如果$u$和$w$共线，那么$C(A)=xu$，即$r(A)\leq1$；  
从行向量组合的角度：$uv^T=\begin{bmatrix}
   av^T\\
   bv^T\\
   cv^T\\
  \end{bmatrix}$，故$uv^T$的行空间$\subset v^T$的行空间$=xv^T$，$wz^T$的行空间$\subset z^T$的行空间$=yz^T$，所以如果$v$和$z$共线，那么行空间就可以合并，即$r(A)\leq1$。
