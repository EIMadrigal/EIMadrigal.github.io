---
title: MIT Linear Algebra#4 Determinants
url: mit-linear-algebra-4
date: 2020-05-29 10:59:00
description: MIT 18.06
categories: Math
tags: [Linear Algebra]
---

行列式本质上是要通过一个数字反应矩阵的某些信息，目前看来不是很重要，快速过一遍。
## 性质
引入是通过3个基本性质：

 1. $det(I)=1$
 2.  交换两行，行列式的值变号：置换矩阵$det(P)=\begin{cases}
1& \text{交换偶数次}\\
-1& \text{交换奇数次}\\
\end{cases}$
 3. $\left|\begin{array}{cccc}
ta & tb\\ 
c & d\\
\end{array}\right|=t\left|\begin{array}{cccc}
a & b\\ 
c & d\\
\end{array}\right|,\left|\begin{array}{cccc}
a+a' & b+b'\\ 
c & d\\
\end{array}\right|=\left|\begin{array}{cccc}
a & b\\ 
c & d\\
\end{array}\right|+\left|\begin{array}{cccc}
a' & b'\\ 
c & d\\
\end{array}\right|$

由3个基本性质可以推出若干：

 1. 若两行相等，则$det(A)=0$  
交换相等的2行，矩阵不变，行列式的值不变，根据性质2：$det(A)=-det(A)$，得证。
 2. 初等行变换不改变行列式的值
$$
\left|\begin{array}{cccc}
a & b\\ 
c-la & d-lb\\
\end{array}\right|=\left|\begin{array}{cccc}
a & b\\ 
c & d\\
\end{array}\right|+\left|\begin{array}{cccc}
a & b\\ 
-la & -lb\\
\end{array}\right|=\left|\begin{array}{cccc}
a & b\\ 
c & d\\
\end{array}\right|
$$
 3. 有一行全为0，行列式为0
 4. **上三角矩阵的行列式等于主对角线元素之积**：$det(U)=d_1d_2...d_n$  
对于任意矩阵，通过初等行变换可以得到$U$，接着向上消元并提出对角线的因子，可以得到$I$。  
这也是Matlab求行列式的方法。
 5. $det(A)=0\Leftrightarrow A是奇异矩阵(消元后有全0行)$  
$det(A)\neq0\Leftrightarrow A可逆\Rightarrow U\Rightarrow d_1d_2...d_n\neq0$
 6. $det(AB)=det(A)*det(B) \Rightarrow det(A^{-1})=\frac{1}{det(A)}$
 7. $det(A^T)=det(A)$，可以通过LU分解去证，这也意味着对行成立的性质对列也成立。

## 计算方法
上一节中介绍了消元化上三角求行列式的方法，本节介绍2种不常用的方法，所有计算都可以通过上一节的3个基本性质获得：  
对于二阶，拆解后有2个非零项：
$$
\left|\begin{array}{cccc}
a & b\\ 
c & d\\
\end{array}\right|=\left|\begin{array}{cccc}
a & 0\\ 
c & d\\
\end{array}\right|+\left|\begin{array}{cccc}
0 & b\\ 
c & d\\
\end{array}\right|=\left|\begin{array}{cccc}
a & 0\\ 
c & 0\\
\end{array}\right|+\left|\begin{array}{cccc}
a & 0\\ 
0 & d\\
\end{array}\right|+\left|\begin{array}{cccc}
0 & b\\ 
0 & d\\
\end{array}\right|+\left|\begin{array}{cccc}
0 & b\\ 
c & 0\\
\end{array}\right|=ad-bc
$$
对于三阶，拆解后有6个非零项：
$$
\left|\begin{array}{cccc} 
a_{11} & a_{12} & a_{13}\\ 
a_{21} & a_{22} & a_{23}\\ 
a_{31} & a_{32} & a_{33}\\ 
\end{array}\right|=\left|\begin{array}{cccc} 
a_{11} &  & \\ 
 & a_{22} & \\ 
 &  & a_{33}\\ 
\end{array}\right|+\left|\begin{array}{cccc} 
a_{11} &  & \\ 
 &  & a_{23}\\ 
 &  a_{32}& \\ 
\end{array}\right|+\left|\begin{array}{cccc} 
 &a_{12}  & \\ 
 a_{21}&  & \\ 
 &  &a_{33}\\ 
\end{array}\right|+\left|\begin{array}{cccc} 
 &a_{12}  & \\ 
 &  &a_{23} \\ 
 a_{31}&  &\\ 
\end{array}\right|+\left|\begin{array}{cccc} 
 &  &a_{13} \\ 
 a_{21}&  & \\ 
 &a_{32}  &\\ 
\end{array}\right|+\left|\begin{array}{cccc} 
 &  &a_{13} \\ 
 &a_{22}  & \\ 
 a_{31}&  &\\ 
\end{array}\right|
$$
接着可以通过交换行得到对角阵并求得结果。  
如果我们观察每一项：从第一行到最后一行，**列下标**是$(1,2,3)$的某个全排列，因此可以知道展开以后非零项一共有$n!$项(第一行有$n$种选择，第二行有$n-1$种选择...)，于是可以得到第二种计算行列式的方法：
$$
det(A)=\Sigma_{n!项}\pm a_{1x}a_{1y}...a_{1w},(x,y,...w)是(1,n)的某个全排列
$$
正负号取决于交换了几次得到$(1,2,3...)$这种朴素的排列。  
举例来看：
$$
\left|\begin{array}{cccc} 
0 & 0 & 1 & 1\\ 
0 & 1 & 1 & 0\\ 
1 & 1 & 0 & 0\\ 
1 & 0 & 0 & 1\\ 
\end{array}\right|
$$
可以先取$(1,3)$位置，接着只能取$(2,2)$，接着$(3,1)$，最后$(4,4)$，所以列的排列是$(3,2,1,4)$，交换一次可得$(1,2,3,4)$，故有一非零项-1；  
还可以先取$(1,4)$，接着$(2,3)$，$(3,2)$，最后$(4,1)$，列的排列是$(4,3,2,1)$，交换2次可得$(1,2,3,4)$，故有一非零项1，除此以外，没有别的选择，所以行列式的值是0。

如果我们对上述拆解三阶行列式的结果提取公因子：
$$
det(A)=a_{11}(a_{22}a_{33}-a_{23}a_{32})+a_{12}(-a_{21}a_{33}+a_{23}a_{31})+a_{13}(a_{21}a_{32}-a_{22}a_{31})
$$
我们穷举了第一行的3种可能的选择$a_{11},a_{12},a_{13}$，对于每种选择，当前行与当前列都不能再用，括号中的式子叫做**代数余子式**$C_{ij}$：去掉$a_{ij}$所在行列的$n-1$阶行列式，并且正负号取决于$i+j$的奇(-)偶(+)。  
这样我们得到了求行列式的第三种方法：
$$
det(A)=a_{11}C_{11}+a_{12}C_{12}+...+a_{1n}C_{1n}
$$
举例来看，对于三对角行列式：
$$
\left|\begin{array}{cccc} 
1 & 1 & 0 & 0\\ 
1 & 1 & 1 & 0\\ 
0 & 1 & 1 & 1\\ 
0 & 0 & 1 & 1\\ 
\end{array}\right|
$$
容易知：$det(A_1)=1,det(A_2)=\left|\begin{array}{cccc}
1 & 1\\ 
1 & 1\\
\end{array}\right|=0,det(A_3)=\left|\begin{array}{cccc}
1 & 1 & 0\\ 
1 & 1 & 1\\
0 & 1 & 1\\
\end{array}\right|=-1$。  
对于四阶，我们**按第一列展开**：$det(A_4)=1*det(A_3)-1*det(A_2)=-1$，此式可以推广：$det(A_n)=det(A_{n-1})-det(A_{n-2})$，可以发现上述三对角行列式是以6为周期的。

## 应用

 - 求逆矩阵  
记得当年矩阵求逆教了一种伴随矩阵的方法：$A^{-1}=\frac{1}{det(A)}C^T$，不知为何物？  
只要证明$AC^T=det(A)I$即可：
$$
\left[\begin{array}{cccc} 
a_{11} & ... & a_{1n}\\ 
... &  & ...\\ 
a_{n1} & ... & a_{nn}\\ 
\end{array}\right]\left[\begin{array}{cccc} 
C_{11} & ... & C_{n1}\\ 
... &  & ...\\ 
C_{1n} & ... & C_{nn}\\ 
\end{array}\right]=\left[\begin{array}{cccc} 
det(A) & ... &0\\ 
... &  & ...\\ 
0 & ... & det(A)\\ 
\end{array}\right]
$$
对于主对角线上的元素：$a_{11}C_{11}+...+a_{1n}C_{1n}=det(A)$；  
对于其它元素：$a_{11}C_{n1}+...+a_{1n}C_{nn}=\left|\begin{array}{cccc} 
a_{11} & ... & a_{1n}\\ 
... &  & ...\\ 
a_{11} & ... & a_{1n}\\
\end{array}\right|=0$。
 - 求解$Ax=b$  
求解：$x=A^{-1}b=\frac{1}{det(A)}C^Tb$，那么考虑$x_1=\frac{1}{det(A)}(b_1c_{11}+..+b_nc_{n1})$，$b_1c_{11}+..+b_nc_{n1}$其实是将矩阵$A$的第一列换为$b$，按照第一列展开求行列式的值即可，同理可以求得其它$x_i$，这种差到没人用的方法竟然被国内教材奉为圭臬。
 - 求体积  
$det(A)$的绝对值可以定义为一个平行六面体的体积，正负表示左手系还是右手系。  
将三阶矩阵$A$的每行(列)当作平行六面体的一条边，如果$A=I$，我们得到一个标准的单位立方体；如果$A=Q$，我们得到一个旋转过的单位立方体，体积仍然为1，可以通过$Q^TQ=I$验证。  
如果是二维情况，那么$det(A)$的绝对值就是平行四边形的面积：
$$
S=\left|\begin{array}{cccc}
a & b\\ 
c & d\\
\end{array}\right|=ad-bc
$$
那么三角形的面积就是$\frac{1}{2}S$，推广到向量的起始位置不在$(0,0)$的情况：
$$
S_{三角形}=\frac{1}{2}\left|\begin{array}{cccc}
x_1 & y_1 & 1\\ 
x_2 & y_2 & 1\\ 
x_3 & y_3 & 1\\ 
\end{array}\right|
$$
可以通过平移到原点去证明。

## 作业
A Hadamard matrix H is a matrix with entries ±1 and orthogonal columns. What is the determinant of H as a function of n? (Hadamard matrices are conjectured to exist for every n that is a multiple of 4, but nobody knows if there is such a matrix even for n=668).  
由于$H$各列正交，故$H^TH=cI$；又$H$的元素只有±1，故$c=n$。所以$det(H)^2=n^n,det(H) = \pm\sqrt{n^n}$，即$n$阶Hadamard矩阵的行列式既可以为正，也可以为负。