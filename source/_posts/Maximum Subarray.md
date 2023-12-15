---
title: Maximum Subarray
url: maximum-subarray
date: 2019-05-05 22:39:00
description: 子数组之和最大值
categories: Computer Science
tags: [Algorithm]
---

## 问题描述
给定一个序列$A_0$、$A_1$、$A_2$、...、$A_{n-1}$，求$A_i+A_{i+1}+...+A_j$的最大值。

## 解一
暴力枚举左端点$i$和右端点$j$，之后计算$A_i$和$A_j$之间的和，时间复杂度$O(n^3)$，很容易TLE。

```
#define INF 0x7FFFFFFF

int sub_sum(int a[],int n)
{
	int MAX = -INF;
	for(int i = 0;i < n;i++)
	{
		for (int j = i; j < n; j++)
 		 {
   			int temp = 0;
   			for (int k = i; k <= j; k++)
  			{
    				temp += a[k];
  			}
   			if (temp > MAX)
   			{
   				MAX = temp;
   			}
 		 }
	}
	return MAX;
}
```

## 解二
输入数据时记录前缀和，预处理$sum[i] = A[0] + ... + A[i]$，因此$A_i+A_{i+1}+...+A_j=sum[j]-sum[i-1]$，复杂度优化为$O(n^2)$。

```
int sub_sum(int a[],int n)
{
	int MAX = -INF;
	for(int i = 0;i < n;i++)
	{
		for(int j = i;j < n;j++}
		{
			int temp = sum[j] - sum[i - 1];
			if(temp > MAX)
				MAX = temp;
			else
				temp = 0;
		}
	}
	return MAX;
}
```

## 解三
动态规划，复杂度$O(n)$。  
定义状态数组$dp[i]$，表示以$A[i]$结尾的连续序列的最大和，这样就只有两种情况：  
1，该连续序列只有$A[i]$这一个元素；  
2，该序列有多个元素，从之前的$A[p]$开始，到$A[i]$结束。  
对于1，最大和就是$A[i]$；  
对于2，最大和是$dp[i - 1]+A[i]$，因为$dp[i]$要求以$A[i]$结尾，所以即使$A[i]$为负数，$dp[i]$仍然等于$dp[i - 1]+A[i]$。  
所以**状态转移方程**就是：
$$dp[i]=max{\{A[i],dp[i-1]+A[i]\}}$$
边界是$dp[0]=A[0]$。  
所以枚举$i$，得到$dp$数组，求出$dp$数组最大值即可。

可以看到，每次计算$dp[i]$只用到$dp[i-1]$，不直接用到之前的信息，这就是状态的**无后效性**，只有这样，动态规划才可能得到正确结果。

```
int dp[5010];
dp[0] = a[0];

int sub_sum(int a[],int n)
{
	for(int i = 1;i < n;i++)
	{
		//状态转移方程
		dp[i] = max(a[i],dp[i - 1] + a[i]);
	}

	int k = 0;
	for(int i = 1;i < n;i++)
	{
		if(dp[i] > dp[k])
			k = i;
	}
	return dp[k];
}
```
为了避免使用$dp[]$数组，可以将空间复杂度优化为$O(1)$：

```
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int allSum = INT_MIN, curSum = 0;
        
        int n = nums.size();
        for(int i = 0;i < n;i++)
        {
            curSum = max(nums[i], curSum + nums[i]);
            if(curSum > allSum)
            {
                allSum = curSum;
            }
        }
        
        return allSum;
    }
};
```
