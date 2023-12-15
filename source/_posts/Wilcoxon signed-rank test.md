---
title: Wilcoxon signed-rank test
url: wilcoxon-signed-rank-test
date: 2021-11-23 14:33:00
description: Wilcoxon符号秩检验
categories: Math
tags: [Probability & Statistics]
---

## Motivation
Wilcoxon符号秩检验是一种非参数检验方法（总体没有高斯分布），t检验貌似要数据服从高斯分布。

具体是这么操作的：
假如有两组数据$X$和$Y$需要检验对比：$(X_i,Y_i)$首先被转为差值$X_i-Y_i$，记为$Z_i$，假设$Z_i\neq 0$且绝对值均不等：
1. 计算$|Z_i|$
2. 将$|Z_i|$排序得排序后的索引$R_i$
3. 检验统计量$T=\sum sgn(Z_i)R_i$
4. 通过对比$T$和原假设下的分布求出p值

如果存在$Z_i=0$，有几种处理方法：
1. `zero_method="wilcox"`：忽略所有等于0的数据
2. `zero_method="pratt"`：排序时考虑为0的项，排完后扔掉这些0项的秩
3. `zero_method="zsplit"`：

## Refs
[Wilcoxon signed-rank test](https://en.wikipedia.org/wiki/Wilcoxon_signed-rank_test)  
[scipy.stats.wilcoxon](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.wilcoxon.html)