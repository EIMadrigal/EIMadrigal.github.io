---
title: Bias-Variance Analysis
url: bias-variance-analysis
date: 2019-03-25 14:48:00
description: 偏差方差分解
categories: Computer Science
tags: [Machine Learning]
---

## Motivation
对于机器学习模型$g$的泛化性能的分析不仅可以通过实验的方式进行评估，还可以从理论上进行分析，这也是learning theory研究的一部分。

## 推导
首先假设无噪，定义在训练集$D$上学习到的模型$g^{(D)}$的期望泛化误差为：
$$
E_{out}(g^{(D)})=E_x[(g^{(D)}(x)-f(x))^2]
$$
先使用小学数学做点预备：
$$
E_D[g^{(D)}(x)]=\bar g(x)\\
E_D[g^{(D)}(x)^2]-\bar g(x)^2=E_D[(g^{(D)}(x)-\bar g(x))^2]=var(x)\\
\bar g(x)^2-2E_D[g^{(D)}(x)]f(x)+f(x)^2=(\bar g(x)-f(x))^2=bias(x)^2
$$
不同的方式会生成不同的训练集$D$，因此$g$的总的期望泛化误差为：
$$
E_D[E_{out}(g^{(D)})]=E_D[E_x[(g^{(D)}(x)-f(x))^2]]=E_x[E_D[(g^{(D)}(x)-f(x))^2]]\\
=E_x[E_D[g^{(D)}(x)^2]-2E_D[g^{(D)}(x)]f(x)+f(x)^2]\\
=E_x[E_D[g^{(D)}(x)^2]-\bar g(x)^2+\bar g(x)^2-2E_D[g^{(D)}(x)]f(x)+f(x)^2]\\
=E_x[var(x)+bias(x)^2]=E_x[var(x)]+E_x[bias(x)^2]=var+bias^2
$$

如果数据有噪，还是用MSE推一个漂亮的分解，其他的损失函数可能没有这么好搞：  
以下推导针对单条测试样例$(x,y)$，其中$y=f(x)+\epsilon,E_D(\epsilon)=0,V_D(\epsilon)=\sigma^2$，噪声只需要均值为0即可，甚至都不需要是高斯分布。
$$
E_D[(g^{(D)}(x)-y)^2]=E_D[(f(x)+\epsilon-g^{(D)}(x))^2]\\
=E_D[\epsilon^2]+E_D[(g^{(D)}(x)-f(x))^2]+E_D[2\epsilon(g^{(D)}(x)-f(x))]\\
=V_D[\epsilon]+E_D[\epsilon]^2+E_D[(g^{(D)}(x)-f(x))^2]+E_D[\epsilon]E_D[2(g^{(D)}(x)-f(x)]\\
=\sigma^2+E_D[(g^{(D)}(x)-f(x))^2]\\
=\sigma^2+E_D[g^{(D)}(x)-f(x)]^2+V_D[g^{(D)}(x)-f(x)]\\
=\sigma^2+(f(x)-E_D[g^{(D)}(x)])^2+V_D[g^{(D)}(x)]\\
=\sigma^2+(\bar g(x)-f(x))^2+var(x)=\sigma^2+bias(x)^2+var(x)
$$
其中测试样例噪声$\epsilon$与$f(x),g^{(D)}(x)$均独立，因此拆为乘积。

可以看到：bias表达的是所有可能的训练数据集训练出的所有模型的平均值与真实值的差异，variance表达的是同等规模的不同的训练数据集学习到的模型之间的差异。  
当训练数据无穷多时，数据扰动对模型的泛化性能没有影响，variance就为0，此时复杂模型的bias通常更低，泛化能力也就更强。  
训练数据较少时，复杂模型的variance很大，此时即使训练误差很低，泛化误差也会很高，即所谓的过拟合。

## Refs
[Bias and Variance](https://nbviewer.org/github/tournami/Learning-From-Data-MOOC/blob/master/Homework%204.html)  
[Learning Theory](https://engineering.purdue.edu/ChanGroup/ECE595/files/chapter4.pdf)
