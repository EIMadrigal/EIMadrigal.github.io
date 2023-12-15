---
title: Maximum Entropy Model
url: maximum-entropy-model
date: 2021-02-07 13:08:00
description: 最大熵模型
categories: Math
tags: [Machine Learning]
---

熵是对随机变量**不确定性**的度量，是对所有可能发生的事件产生的信息量的期望，没有外部能量输入的情况下，封闭系统趋向熵增。

信息熵指离散随机事件的出现概率：$X={x_1,x_2,...,x_n}$，$P(X=x_i)=p_i$
$$
H(X)=-\sum_{i=1}^{n}p(x_i)log\ p(x_i)
$$

Joint Entropy 
$$
H(X,Y)=-\sum_{i=1}^{n}\sum_{j=1}^{m}p(i,j)log\ p(i,j)
$$

$$
H(X|y_j)=-\sum_{i=1}^{n}p(x_i|y_j)log\ p(x_i|y_j)
$$

按照$Y$的各种情况进行加权平均，得条件熵$H(X|Y)$
$$
H(X|Y)=-\sum_{i=1}^{n}\sum_{j=1}^{m}p(y_j)p(x_i|y_j)log\ p(x_i|y_j)=-\sum_{i=1}^{n}\sum_{j=1}^{m}p(x_i,y_j)log\ p(x_i|y_j)
$$
易证$H(X|Y)=H(X,Y)-H(Y)$

交叉熵，$P(X)$和$Q(X)$是$X$的两个概率分布
$$
D_{KL}(P\ ||\ Q)=\sum_xP(x)log\frac{P(x)}{Q(x)}
$$

互信息
$$
I(X,Y)=\sum_x\sum_yp(x,y)log\frac{p(x,y)}{p(x)p(y)}
$$
互信息就是联合分布$P(X,Y)$和独立分布乘积$P(X)P(Y)$的交叉熵。  
易证$I(X,Y)=H(X)+H(Y)-H(X,Y)$

直观上看：在已知部分知识的前提下，对于未知分布最合理的推断就是符合已知且最不确定的推断，整个系统趋向于无序，熵最大。  
在一定**约束条件**下，使得$H(X|Y)$最大。
$$
p^*={\underset {p\in P}{\operatorname {arg\,max} }}\,-\sum_{i=1}^{n}\sum_{j=1}^{m}\bar p(y_j)p(x_i|y_j)log\ p(x_i|y_j)
$$
约束条件：
$$
\sum_xp(x|y)=1 \\
...
$$
又可以通过拉格朗日乘数法变为对偶问题求解。

由于无法求得解析解，只能用迭代法求数值解：
$$
p^*(x|y)=\cfrac{1}{Z_\lambda(y)}e^{\sum_i\lambda_if_i(x,y)} \\
Z_\lambda(y)=\sum_xe^{\sum_i\lambda_if_i(x,y)}
$$