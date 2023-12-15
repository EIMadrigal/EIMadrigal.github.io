---
title: Dynamic Programming
url: dynamic-programming
date: 2020-01-06 21:04:00
description: 看了很多资料，依然学不会DP
categories: Computer Science
tags: [Algorithm]
---

DP是算法学习中非常重要的一种思想，关于动态规划的解释，可以参考[这篇文章](https://www.zhihu.com/question/23995189/answer/613096905)。

## 概念
使用DP三个条件：

 1. 最优子结构：待解决的问题可以被分解为若干子问题，并且递归地找到子问题的最优解，从而得到全局最优解；
 2. 重叠子问题：在解决子问题的过程中，很多子问题都会被求解多次，第一次计算后存储该子问题的解，以后就可以直接使用，即降低了时间复杂度。如果子问题没有重叠，那么这就是**分治**的问题；
 3. 无后效性：子问题的最优解是确定的，完全可以用来解决更大的子问题。

DP一般有两种形式：

 - Top-down/memoization: 记忆化递归可能stackoverflow
 - Bottom-up：迭代计算

模板：

```cpp
// 记忆化递归
unordered_map<int, int> hash;  // memory dict
int f(i, j, ...) {
	if base_case(i, j)
		return ...;
	if (i, j) not in hash
		hash[(i, j)] = f(...);
	return hash[(i, j)];
}
return f(n, m);

// DP
int dp[][];   // need padding sometimes
dp[0][0] = ...;   // base case
for(int i = 0;i < n;++i)
	for(int j =  0;j < m;++j)
		dp[i][j] = ...  // 状态转移
return dp[n][m];
```

## 分类

 - 基础题：LeetCode 509/70/746/62/63/343/96/[Fibonacci](https://www.cnblogs.com/EIMadrigal/p/11478906.html)
 - 背包问题：
	 - [0/1 Knapsack](https://www.cnblogs.com/EIMadrigal/p/12345051.html)：LeetCode 416/1049/494/474
	 - [Unbounded Knapsack](https://www.cnblogs.com/EIMadrigal/p/12345051.html)：LeetCode 518/377/70/322/279/139
 - House Thief：LeetCode 198/213/337
 - 股票问题：LeetCode 121/122/123/188/309/714
 - Longest Common Substring/Subsequeunce：LeetCode 300/1143/1035/674/718/53/392/115/583/72/647/516

## 步骤
一般来讲，都是通过暴力->记忆化递归->Bottom-up三部曲，当然熟悉后可以快速判断这是一个DP问题，然后直接写出Bottom-up的解法。  
我个人认为最难的一步在于判断出你的暴力解法满足DP的性质（你要能认出来这是一个DP问题），可以用DP去优化暴力解法。

 - 确定问题分类
 - 确定状态：需要几个变量来跟踪目前的状态，一般来讲至少需要index，因为这决定了我们已经考虑过了哪些值，没考虑哪些值，正在考虑哪些值。选定的变量组合要能唯一确定一个状态
 - 状态转移：为了达到base case，当前状态怎么才能由之前的状态得到。也就是Top-down逐渐分解问题，每一次递归调用都会分解一下
 - base case：一般比较简单，不废话了
 - code：思路清楚了，也不难
 - 优化：一般优化空间复杂度

## Ref
[DP IS EASY! 5 Steps to Think Through DP Questions.](https://leetcode.com/problems/target-sum/discuss/455024/DP-IS-EASY!-5-Steps-to-Think-Through-DP-Questions.)
