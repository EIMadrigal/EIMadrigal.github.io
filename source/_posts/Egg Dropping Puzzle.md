---
title: Egg Dropping Puzzle
url: egg-dropping-puzzle
date: 2020-03-22 14:26:00
description: 扔鸡蛋问题
categories: Computer Science
tags: [Algorithm]
---

## The Two Egg Problem
曾经是Google的一道经典题。  
题意：有一个百层高楼，鸡蛋在$L$层及以下扔都不碎，在$L$层以上都会碎。现在某人有$k$个鸡蛋，问在**最坏情况下**，至少扔多少次(用$m$表示)可以确定$L$的值。  
分析：先来考虑$k=1$的情况。只有1个鸡蛋，为了得到一个确定的$L$，只能从第一层开始，逐渐尝试增加楼层高度，因此$m=100$时，无论$L$的值是多少，都可以被确定。  
再来考虑$k=\infty$的情况。这种情况就变为了binary search的问题，先拿一个在50层扔，如果碎，则在25层扔；如果不碎，则在75层扔...即$m=7$。  
最后来考虑$k=2$的情况。因为$k=1$只能一层一层试，所以第一个鸡蛋应该尽可能缩小搜索空间，但是如果第一个鸡蛋的楼层间隔太小(比如在2层、4层...)，无疑会增加$m$。不妨取第一个鸡蛋在10层、20层...，共10次；假如第一个在10层没碎，在20碎了，那么第二个鸡蛋可以尝试11、12...19，共9次；故$m=19$。  
上面方案的问题在于：如果临界楼层比较高，那么第二个鸡蛋的次数是确定的，但第一个就需要多试几次，总次数就会增加。  
那么如何使得不论临界楼层在哪，$m$的值都不会波动呢？  
很简单，只要第一个多扔一次，确定的范围(第二个要试的次数)减小，总次数就会均衡。  
对于第一个鸡蛋，第一次在$a$层扔，如果不碎，第二次向上增加$a-1$层...直到最后只向上增加$1$层：$a+(a-1)+...+1\geq100$，故$a\geq13.7$。  
鸡蛋一：在14层、27层、39层、50层、60层、69层、77层、84层、90层、95层、99层、100层扔，共12次；  
鸡蛋二：如果蛋一在14层碎了，蛋二要扔13次，共14次；如果蛋一在27层碎了，蛋二要扔12次，共14次...故$m=14$。

## Super Egg Problem
对于$k=2$，我们有了一个比较好的解决方案。那么现在有$k$个鸡蛋，楼高$n$层，[问题](https://leetcode.com/problems/super-egg-drop/)(记作$m(k,n)$)又该如何解决？  
$k=1$和$n=1$的情况比较简单：
|n\k|1|2|3|4|...|
|--|--|--|--|--|--|
|1|1|1|1|1|
|2|2|
|3|3|
|...|  |
那么如果我们递归地思考：任选一层$h$扔第一个鸡蛋，无非有碎和不碎2种情况：  
碎：临界楼层在1~h之间，问题规模缩小为$m(k-1,h-1)$；  
不碎：临界楼层在h~n之间，问题规模缩小为$m(k,n-h)$。  
所以：$m_h(k,n)=1+max\{ m(k-1,h-1),m(k,n-h)\}$。  
对于$h$，可以采用枚举的方法，计算$m_h(k,n)$，在其中选出一个最小的值，故问题得到解决：
$$m(k,n)=min\{m_h(k,n)\},h=1,2,...n$$
Base Case就是$k=1$和$n=1$。  
时间复杂度$O(KN^2)$，空间复杂度$O(KN)$。  
记忆化递归：

```cpp
class Solution {
public:
    int superEggDrop(int K, int N) {
        vector<vector<int>> memo(K + 1, vector<int>(N + 1, 0));
        return helper(K, N, memo);
    }
private:
    int helper(int K, int N, vector<vector<int>>& memo) {
        if(K == 1) {
            return N;
        }
        if(N <= 1) {
            return N;
        }
        if(memo[K][N]) {
            return memo[K][N];
        }
        
        int ans = INT_MAX;
        for(int i = 1;i <= N;++i) {
            ans = min(ans, 1 + max(superEggDrop(K - 1, i - 1), superEggDrop(K, N - i)));
        }
        memo[K][N] = ans;
        return ans;
    }
};
```
上述最直观的解法复杂度太高，无法通过Leetcode的数据。如何优化呢？  
$m(K,N)$表示该问题的解，如果鸡蛋数$K$固定，随着楼层数$N$增加，问题的解一定是增加的。  
我们又把问题分解为了2个子问题$m(k-1,h-1)$和$m(k,n-h)$，$m(k-1,h-1)$随$h$单调递增，$m(k,n-h)$单调递减([图源](https://zhuanlan.zhihu.com/p/92288604))：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200319150302530.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0VJTWFkcmlnYWw=,size_16,color_FFFFFF,t_70)  
此时$min(max(...))$就是求中间的折点，可以使用Binary Search，时间复杂度降为$O(KNlgN)$：

```cpp
class Solution {
public:
    int superEggDrop(int K, int N) {
        vector<vector<int>> memo(K + 1, vector<int>(N + 1, 0));
        return helper(K, N, memo);
    }
private:
    int helper(int K, int N, vector<vector<int>>& memo) {
        if(K == 1) {
            return N;
        }
        if(N <= 1) {
            return N;
        }
        if(memo[K][N]) {
            return memo[K][N];
        }
        
        int ans = INT_MAX;
        int l = 1, r = N + 1;
        while(l < r) {
            int m = l + (r - l) / 2;
            int broken = helper(K - 1, m - 1, memo), noBroken = helper(K, N - m, memo);
            if(broken > noBroken) {
                r = m;
                ans = min(ans, 1 + broken);
            }
            else {
                l = m + 1;
                ans = min(ans, 1 + noBroken);
            }
        }
        
        memo[K][N] = ans;
        return ans;
    }
};
```
当然，此题还有$O(KN)$的做法，甚至还有一种数学做法可以达到$O(KlgN)$的时间复杂度和$O(1)$的空间复杂度，由于本人水平实在有限，就不再探索。