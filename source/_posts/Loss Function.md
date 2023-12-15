---
title: Loss Function
url: loss-function
date: 2021-03-13 19:08:00
description: 损失函数
categories: Computer Science
tags: [Machine Learning]
---

For binary classification (+1, -1), if we classify correctly then $y\cdot f = y\cdot \theta^Tx\gt0$; otherwise $y\cdot f = y\cdot\theta^Tx\lt0$. Thus we have following loss functions:

 - 0/1 loss  
$\min_\theta\sum_i L_{0/1}(\theta^Tx)$. We define $L_{0/1}(\theta^Tx) =1$ if $y\cdot f \lt 0$, and $=0$ o.w. Non convex and very hard to optimize.
 - Hinge loss  
Upper Bound of 0/1 loss. Approximate 0/1 loss by $\min_\theta\sum_i H(\theta^Tx)$. We define $H(\theta^Tx) = max(0, 1 - y\cdot f)$. Apparently $H$ is small if we classify correctly.
 - Logistic loss  
$\min_\theta \sum_i log(1+\exp(-y\cdot \theta^Tx))$.
 
Fortunately, hinge loss, logistic loss and square loss are all convex functions. Convexity ensures global minimum and it's computationally appealing.
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210226181824674.png)
Figure 7.5 from Chris Bishop's PRML book. The Hinge Loss E(z) = max(0,1-z) is plotted in blue, the Log Loss in red, the Square Loss in green and the 0/1 error in black.

From the figure we can observe that the hard instance (near the boundary) will influence the loss function a lot so we need to make the model robust and can deal with the hard ones.

For binary classification we can unify the two cases (classify correctly or not) by $y\cdot f$, but for multi-class classification (0, 1, 2, ..., k) we cannot unify all the cases. So we use cross-entropy as the loss.

There exists a vivid example for transform the target function: If a noisy picture is given, and want to output the clean one. Here the clean one is hard to control so we can let the noise be the target function and wo should minimize the amplitude of the noise. Thus the problem becomes controllable. 