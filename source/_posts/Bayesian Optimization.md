---
title: Bayesian Optimization
url: bayesian-optimization
date: 2021-04-06 22:18:00
description: 贝叶斯优化
categories: Computer Science
tags: [AutoML]
---

## Motivation
Hyper-parameters tuning has become an important work during training neural networks. As the number of Hyper-parameter is becoming larger, researchers proposed Grid Search & Random Search to wish to get better combinations of Hyper-parameters. However, Grid Search has a high time cost. Although some experiments showed that Random Search got a better result than  Grid Search but the result is still not fulfilling.

Besides, there are some gradient-based methods to solve the problem. But the objective function is usually not differentiable or even not continuous. Thus these methods have a very finite usage.

BO is a gradient-free optimization method to get global solutions of a black-box function. The function usually has a high cost to compute such as training a deep neural network after tuning the Hyper-parameters. For this reason, we usually find a **surrogate** function to approximate the original function $f$. In the field of AutoML, we often use  Gaussian Process, Random Forest or deep network as the surrogate model. The simplest form of BO is as follows:
![在这里插入图片描述](1.png)
$f$ represents the black-box function that we want to optimize (black-box means that the function transforms a configuration $x$ to an output but we don't know the exact function relationship). $\chi$ represents the search space of the combination of hyper-parameters. $S$ represents **Acquisition Function** which is used to select the promising $x$. $M$ represents the surrogate model which takes a configuration $x$ and outputs the performance (much like $f$ does).

First we need to get some samples from $(f,\chi)$, thus we get $D=(x_i,f(x_i)), i=1...n$.

Next we iterate $T$ times (often fixed) to select configuration $x$. Use the dataset $D$ to train the surrogate model $M$ (much easier than train $f$). $M$ has several choices such as Random Forest, Tree Parzen Estimators. Here we use GP so we get the probabilistic model $p(y|x,D)$.

Then we need to find the most promising configuration $x$. The most important thing for Acquisition Function is to make a balance between **exploration & exploitation**. It means that when selecting the next $x$ we not only want to select those untried points (exploration) but also want to select those tried points which has a great $f(x)$ (exploitation). 

Finally use the promising $x_i$ to get corresponding performance $y_i$ and join the pair into $D$.

## Gaussian Process
If we assume $x_i$ is independent with each other, the Multivariant Gaussian Distribution's probability density is as follows:
$$
p(x_1,...,x_n)=\frac{1}{(2\pi)^{\frac{n}{2}}\sigma_1...\sigma_n}exp(-\frac{1}{2}[\frac{(x_1-\mu_1)^2}{\sigma_1^2}+...+\frac{(x_n-\mu_n)^2}{\sigma_n^2}])
$$
We can rewrite the formula to the vectorized version:
$$
p(x)=(2\pi)^{-\frac{n}{2}}|K|^{-\frac{1}{2}}exp[-\frac{1}{2}(x-\mu)^TK^{-1}(x-\mu)]
$$
in which
$$
K=\left[
\begin{matrix}
  \sigma_1^2     & 0      & \cdots & 0      \\
 0      & \sigma_2^2      & \cdots & 0      \\
 \vdots & \vdots & \ddots & \vdots \\
 0     & 0      & \cdots & \sigma_n^2     \\
\end{matrix}
\right], x-\mu=[x_1-\mu_1,...,x_n-\mu_n]^T
$$
Thus $x\sim N(\mu,K)$, $\mu$ is the mean vector and $K$ is the covariance matrix (a diagonal matrix since the independence).

But what should we do when $x$ has infinite dimensions? Such as in a continuous temporal T or spatial S. Actually GP means Gaussian Distribution and Stochastic Process (about time T). GP is defined by an infinite number of Random Variables on a continuous domain. In other words, it is an infinite dimension Gaussian Distribution. Formally, let's sample n moments from T: $t_1,...,t_n\in T$, thus we get a n-dimensional vector $(\xi_1,...,\xi_n)$, if this vector is a n-dimensional Gaussian Distribution then ${\xi_t}$ is a GP.

Let's take an easy example to illustrate: suppose during peoples' life time, at every moment $t$ the energy of the population forms a Gaussian Distribution but different moments have different $\mu$ and $\sigma$:
![在这里插入图片描述](2.png)
If we take 5 moments during a population's life time, then $\xi_1-\xi_5$ all forms Gaussian Distribution but they have different $\mu$ and $\sigma$. If we sample an arbitrary moment $t$ then $\xi(t)\sim N(\mu_t,\sigma_t^2)$. If we sample at some points and connect them together we get two samples of the GP, as the figure shows.

Now that we know what happens at $t$, let's consider the whole $T$. We know that for a finite Gaussian Distribution, it can be determined by a n-dimensional vector $\mu_n$ (reflects every Random Variable's expectation) and a $n\times n$ matrix $\Sigma$ (reflects every RV's variances and covariance between different dimensions). It is almost the same for GP except that we cannot use a vector to describe every $t$'s mean since it is infinite. So we need a function $m(t)$ to describe the continuous $T$. For $\Sigma$ we should use a kernel function $k(s,t)$ to describe the covariance between time $t$ and $s$. Once $m(t)$ and $k(s,t)$ is defined the GP is determined $\xi_t\sim GP(m(t),k(s,t))$.

The most popular kernel function is RBF which is defined as follows:
$$k(s,t)=\sigma^2exp(-\frac{||s-t||^2}{2l^2})$$
$\sigma$ and $l$ are two hyper-parameters. If $s$ and $t$ are close in $T$ then the output covariance will be larger and it means the correlation between the two points is bigger.

Once we have some knowledge about GP we can start to know Gaussian Process Regression, which is a kind of Probabilistic Model. It means that we can use Prior and Observations to calculate Posterior. First we define a GP by $m(t)$ and $k(s,t)$, which is a Prior. Then we observe some data to revise the GP's $m(t)$ and $k(s,t)$ to get Posterior. But how?

Here we need to use some Gaussian Distribution's nice properties: Once Gaussian always Gaussian. It means that marginal distribution, summation and conditional distribution of a GD are still GD. Assume a n-dimensional RV $x\sim N(\mu,\Sigma)$ and we divide it into two parts $x_A$ and $x_B$ then we get:
$$x=\begin{bmatrix} x_A\\ x_B \end{bmatrix},\mu=\begin{bmatrix} \mu_A\\ \mu_B \end{bmatrix},\Sigma=\begin{bmatrix} \Sigma_{AA}, \Sigma_{AB} \\ \Sigma_{BA}, \Sigma_{BB} \end{bmatrix}$$
Then we can get:
$$x_A|x_B\sim \mathcal{N}(\mu_A+\Sigma_{AB}\Sigma_{BB}^{-1}(x_B-\mu_B),\Sigma_{AA}-\Sigma_{AB}\Sigma_{BB}^{-1}\Sigma_{BA})$$
Thus we could update the GD's Posterior parameters. It is much the same in GP. If we get some samples $(X,Y)$ then the rest is $(X^*,f(X^*))$. The joint distribution forms an infinite GD:
$$\begin{bmatrix} Y\\ f(X^*) \end{bmatrix}\sim N(\begin{bmatrix} \mu(X)\\ \mu(X^*) \end{bmatrix},\begin{bmatrix} k(X,X), k(X,X^*) \\ k(X^*,X), k(X^*,X^*) \end{bmatrix})$$
So we want to know the rest of the points based on the observed points: $f(X^*)|Y\sim N(\mu^*,k^*)$.
$$\mu^*=\mu(X^*)+k(X^*,X)k(X,X)^{-1}(Y-\mu(X))\\
k^*=k(X^*,X^*)-k(X^*,X)k(X,X)^{-1}k(X,X^*)$$
Here is an example:
![在这里插入图片描述](3.png)
Finally let's return back to our BO's $M$. We first assume our prior: $\mu(X)=0,k(X,X^*)=RBF$. Plus the observed and evaluated $D=\{x_i,y_i\}$ we can get $\hat \mu$ and $\hat{\sigma}^{2}$, then the posterior prediction is $p(y|x,D)$, which is still a Gaussian Distribution. The calculation process is as follows:
$$
y=(y_1,...,y_i)^T \\
\hat \mu=k^T(x)(k+\sigma_{n}^{2}I)^{-1}y \\
\hat{\sigma}^{2}=k(x^*x)-k(x)^T(k+\sigma_{n}^{2}I)^{-1}k(x)
$$
Once we get the posterior prediction $p(y|x,D)$, we can feed them to the Acquisition Function to get next $x_t$.

## Acquisition Function
There are some popular Acquisition Functions:

 1. Upper Confidence Bound (UCB)
$x_{t+1}=\underset{x\in X}{\operatorname{arg\ max}}[\mu_{t}(x)+\beta_t^{1/2}\sigma_t(x)]$
The weighted sum of posterior mean and posterior standard deviation. The two items correspond exploitation and exploration,  respectively.
 2. Expected Improvement (EI)
$x_{t+1}=\underset{x\in X}{\operatorname{arg\ max}}\ E_{f(x)\sim N(\mu_{t}(x),\sigma_t^2(x))}[max(0,f(x)-f_t^+)]$, $f_t^+$ is the max observation value during the first $t$ iterations.

Except the above functions, there are Probability of Improvement, Entropy Search and so on.

## Reference
[A Visual Exploration of Gaussian Processes](https://jgoertler.com/visual-exploration-gaussian-processes/)  
[如何通俗易懂地介绍Gaussian Process](https://www.zhihu.com/question/46631426)  
[贝叶斯优化/Bayesian Optimization](https://zhuanlan.zhihu.com/p/76269142)  
[Exploitation vs Exploration](https://github.com/fmfn/BayesianOptimization/blob/master/examples/exploitation_vs_exploration.ipynb)  
[BayesianOptimization](https://github.com/fmfn/BayesianOptimization)  
[Lecture 15: Gaussian Processes](https://www.cs.cornell.edu/courses/cs4780/2018fa/lectures/lecturenote15.html)  
[Exploring Bayesian Optimization](https://distill.pub/2020/bayesian-optimization/)
