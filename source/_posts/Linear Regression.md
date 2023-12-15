---
title: Linear Regression
url: linear-regression
date: 2019-06-03 22:54:00
description: 线性回归
categories: Computer Science
tags: [Machine Learning]
---

## 线性回归模型
$$h_{\theta}(x)=\sum_{i=0}^{d}\theta_ix_i=\theta^Tx=x^T\theta$$
其中，$x_0=1$，$d$表示$x$的特征数量。

在给定训练集下，需要用学习算法确定参数$\theta$，使得模型的预测值$h(x)$与真实值$y$尽可能接近。为了精确地描述这种接近程度，定义损失函数：
$$J(\theta)=\frac{1}{2}\sum_{i=1}^{n}(h_{\theta}(x^{(i)})-y^{(i)})^2$$
问题转化为选择一组$\theta$使得$J(\theta)$最小，这里有2种求解方法：

 1. 梯度下降
 2. Normal Equation

## 梯度下降
梯度下降的motivation非常直观，首先随机选择一组参数$\theta$，接着沿$J(\theta)$下降最快的方向更新$\theta$，经过若干次迭代就有望找到令$J(\theta)$收敛的参数$\theta$：
$$\theta_j=\theta_j-\alpha\frac{\partial}{\partial\theta_j}J(\theta)$$
将$J(\theta)$的偏导数代入即得到所谓的batch gradient descent更新规则：
$$\theta_j=\theta_j-\alpha\sum_{i=1}^{n}(h_\theta(x^{(i)})-y^{(i)})x_j^{(i)}$$
其向量化表示为：
$$\theta=\theta-\alpha\sum_{i=1}^{n}(h_\theta(x^{(i)})-y^{(i)})x^{(i)}$$
由于损失函数$J(\theta)$是凸二次函数，因此总能收敛到唯一的全局最小值。

batch gradient descent一次更新需要计算所有训练样本，开销较大，因此有同学提出了stochastic gradient descent，每遇到一个训练样本就进行一次参数更新：
$$\theta=\theta-\alpha(h_\theta(x^{(i)})-y^{(i)})x^{(i)}$$
stochastic gradient descent一般比batch gradient descent收敛快，但是有可能在$J(\theta)$的最优点附近振荡，永远无法收敛到精确最优。不过一般选择最优点附近的参数也可以接受，还可以通过递减学习率$\alpha$确保其精确收敛。

值得一提的是：梯度下降算法存在“锯齿”效应，因此为了加速收敛，通常要进行归一化处理使得不同特征的尺度相近。

## Normal Equation
除了用迭代的方式求解$J(\theta)$的最小值，还可以用数学工具直接求得闭式解。

为了简洁地表示后续求导，使得人生不要太过凌乱，我们首先研究下$J(\theta)$的向量表示：  
假设训练集$X$和对应的标签$y$分别为：
$$X=\left[
\begin{matrix}
 (x^{(1)})^T \\
 (x^{(2)})^T \\
 \vdots \\
 (x^{(n)})^T \\
\end{matrix}
\right],y=\left[
\begin{matrix}
 y^{(1)} \\
 y^{(2)} \\
 \vdots \\
 y^{(n)} \\
\end{matrix}
\right]
$$
由于$h_{\theta}(x^{(i)})=(x^{(i)})^T\theta$，所以有：
$$X\theta-y=\left[
\begin{matrix}
 (x^{(1)})^T\theta \\
 (x^{(2)})^T\theta \\
 \vdots \\
 (x^{(n)})^T\theta \\
\end{matrix}
\right]-\left[
\begin{matrix}
 y^{(1)} \\
 y^{(2)} \\
 \vdots \\
 y^{(n)} \\
\end{matrix}
\right]=\left[
\begin{matrix}
 h_{\theta}(x^{(1)})-y^{(1)}  \\
 h_{\theta}(x^{(2)})-y^{(2)} \\
 \vdots \\
 h_{\theta}(x^{(n)})-y^{(n)} \\
\end{matrix}
\right]$$
根据向量运算法则$x^Tx=\sum_ix_i^2$，终于得到了$J(\theta)$的简单点的表示：
$$\frac{1}{2}(X\theta-y)^T(X\theta-y)=\frac{1}{2}\sum_{i=1}^{n}(h_{\theta}(x^{(i)})-y^{(i)})^2=J(\theta)$$
利用高中数学导数的知识，只要求得$J(\theta)$关于参数$\theta$的导数并令其为0，就大功告成了：
$$\nabla_{\theta}J(\theta)=\frac{1}{2}(X\theta-y)^T(X\theta-y)\\=\frac{1}{2}\nabla_{\theta}[(X\theta)^TX\theta-(X\theta)^Ty-y^T(X\theta)+y^Ty]=\frac{1}{2}\nabla_{\theta}[\theta^T(X^TX)\theta-y^T(X\theta)-y^T(X\theta)]\\=\frac{1}{2}\nabla_{\theta}[\theta^T(X^TX)\theta-2(X^Ty)^T\theta]=\frac{1}{2}(2X^TX\theta-2X^Ty)=X^TX\theta-X^Ty$$
哦，高中数学好像不太够，还要知道$a^Tb=b^Ta,\nabla_{x}Ax=A^T,\nabla_{x}x^TAx=(A+A^T)x$。

结束了无聊的数学推导，所谓的Normal Equation就来了：
$$X^TX\theta=X^Ty$$
我们暂时先不考虑$X^TX$不可逆的情况，最终的解析解就是$\theta=(X^TX)^{-1}X^Ty$。这种方法不需要做Feature Scaling，但是只能用于容易求解的模型。

## Probabilistic view
当观测数据满足一些假设条件时，就可以自然而然地推导出均方误差形式的损失函数。

假设观测数据满足：
$$y^{(i)}=\theta^Tx^{(i)}+\epsilon^{(i)}$$
其中，$\epsilon^{(i)}$表示偏差项，并且$\epsilon^{(i)}$服从IID的高斯分布，即$\epsilon^{(i)}\sim \mathcal{N}(0, \sigma^2)$。

在满足上述假设的条件下，给定$x^{(i)}$，观测到的$y^{(i)}$满足概率分布$y^{(i)}|x^{(i)};\theta\sim \mathcal{N}(\theta^Tx^{(i)}, \sigma^2)$，即：
$$p(y^{(i)}|x^{(i)};\theta)=\frac{1}{\sqrt{2\pi }\sigma}exp(-\frac{(y^{(i)}-\theta^Tx^{(i)})^2}{2 \sigma^2})$$
我们希望选择合适的参数$\theta$，使得在整个训练集上最大化观测数据出现的概率，也就是所谓的极大似然估计：
$$\prod_{i=1}^{n}p(y^{(i)}|x^{(i)};\theta)=\prod_{i=1}^{n}\frac{1}{\sqrt{2\pi }\sigma}exp(-\frac{(y^{(i)}-\theta^Tx^{(i)})^2}{2 \sigma^2})=L(\theta)$$
To make our life easier，采用对数似然函数的形式去求$L(\theta)$的最大值：
$$l(\theta)=log\ L(\theta)=nlog\ \frac{1}{\sqrt{2\pi }\sigma}-\frac{1}{2\sigma^2}\sum_{i=1}^n(y^{(i)}-\theta^Tx^{(i)})^2$$
因此，最大化$L(\theta)$与最小化$J(\theta)=\frac{1}{2}\sum_{i=1}^{n}(h_{\theta}(x^{(i)})-y^{(i)})^2$等价，也就证明了均方误差损失函数的合理性。

值得一提的是：上述假设并不唯一，存在其它合理的假设同样能够证明均方误差作为损失函数的合理性。

## 局部加权线性回归
在朴素的线性回归中，训练模型得到的参数$\theta$是固定的，对于每个要预测的点$x$计算$\theta^Tx$就完事了。这种参数化的学习算法在预测时不需要训练数据的支持，非常快捷。

局部加权线性回归的motivation在于：朴素线性模型强行拟合所有训练样本，因为模型简单往往欠拟合。对于任意一个样本$x$，如果只根据其周围几个样本来建立局部的线性模型，且距离$x$越近其在损失函数中的权值越大，就得到了所谓的Locally Weighted Linear Regression：
$$J(\theta)=\frac{1}{2}\sum_{i=1}^{n}w^{(i)}(h_{\theta}(x^{(i)})-y^{(i)})^2$$
直观上看：如果一个点权值较大，其对损失函数的贡献就越大；如果权值较小，那么该点基本可以忽略不计。

权值一般会设计为指数函数：
$$w^{(i)}=exp(-\frac{(x^{(i)}-x)^T(x^{(i)}-x)}{2\tau^2})$$
其中，$x$表示待测试样本，$\tau$负责控制随距离增加权值的衰减快慢。

另外，与kNN类似，LWR也是一种懒惰学习算法，即只有给出测试样例时才会训练并预测。因此，这种非参数算法在预测时需要存储训练集，并且参数数量会随训练集大小线性增长。