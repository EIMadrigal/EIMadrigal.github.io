---
title: Longest XXX
url: longest-xxx
date: 2020-03-11 10:19:00
description: Some DP Problems
categories: Computer Science
tags: [Algorithm]
---

## Longest Common Substring

 - Brute Force  
遍历`a`和`b`所有位置的组合，向后延伸，直到遇到两个不同的字符，复杂度是$n^3$级别。
```cpp
class Solution {
    public:
    // 返回所有结果
    vector<string> longestCommonSubstring(string& a, string& b) {
        vector<string> ans;
        int maxLen = 0;
        for (int i = 0; i < a.size(); ++i) {
            for (int j = 0; j < b.size(); ++j) {
                string cur;
                for (int m = i, n = j; m < a.size() && n < b.size(); ++m, ++n) {
                    if (a[m] != b[n]) {
                        break;
                    }
                    cur += a[m];
                }
                if (cur.size() && cur.size() >= maxLen) {
                    maxLen = cur.size();
                    ans.push_back(cur);
                }
            }
        }
        return ans;
    }
};
```
 - DP  
暴力解有很多重复计算：比如以$i$和$j$为起点去向后延伸，我们可能需要比较$i+1$和$j+1$、$i+2$和$j+2$...而以$i+1$和$j+1$为起点时，仍然要比较$i+2$和$j+2$，**重叠子问题**给动态规划带来了可能。  
暴力做法是将每个$i$和$j$作为起点，现在我们考虑将$i$和$j$作为终点，令$L(i,j)$表示text1[0...i]和text2[0...j]中的最长子串的长度（不是非要以$i$和$j$作为结尾）：
$$L(i,j)=
\begin{cases}
1+L(i-1,j-1)& \text{a[i]=b[j]}\\
0& \text{a[i]!=b[j]}
\end{cases}$$
为了简便，假设下标从1开始，那么边界条件：$L(0,j)=0,L(i,0)=0$。

```cpp
class Solution {
public:
	int longestCommonSubstring(string& a, string& b) {
		vector<vector<int>> L(1 + a.size(), vector<int>(1 + b.size(), 0));
		int maxLen = 0;

		for (int i = 1; i <= a.size(); ++i) {
			for (int j = 1; j <= b.size(); ++j) {
				if (a[i - 1] == b[j - 1]) {
					L[i][j] = 1 + L[i - 1][j - 1];
				}
				else {
					L[i][j] = 0;
				}
				maxLen = max(maxLen, L[i][j]);
			}
		}
		
		return maxLen;
	}
};

```
显然时间降为$O(n^2)$，空间升为$O(n^2)$。仔细观察，计算$L(i,j)$只需要左上方$L(i-1,j-1)$的信息，所以我们按照斜线方向计算，可以将空间优化到$O(1)$：

```cpp
class Solution {
public:
	int longestCommonSubstring(string& a, string& b) {
		// from the up-right corner
		int row = 0, col = b.size() - 1;
		int maxLen = 0;

		while (row < a.size()) {
			int curLen = 0;
			for (int i = row, j = col; i < a.size() && j < b.size(); ++i, ++j) {
				if (a[i] == b[j]) {
					++curLen;
				}
				else {
					curLen = 0;
				}
				maxLen = max(maxLen, curLen);
			}
			if (col > 0) {  
				--col;  // 斜线左移
			}
			else {
				++row;   // 斜线下移
			}
		}
		
		return maxLen;
	}
};

```
(TODO)输出所有的最长公共子串

## Longest Common Subsequence
实现见[Github](https://github.com/EIMadrigal/LeetCode/blob/master/DP/%E5%AD%90%E5%BA%8F%E5%88%97%E7%B3%BB%E5%88%97.md)

 - Brute Force  
找到`a`的所有子序列，判断是否是`b`的子序列，指数级复杂度，也就没有写出来的必要了。
 - DP  
重叠子问题很明显，而且LCS具有最优子结构，令$L(i,j)$表示text1[0...i]和text2[0...j]的LCS长度（不是非要以$i$和$j$作为结尾）：
$$L(i,j)=
\begin{cases}
1+L(i-1,j-1)& \text{a[i]=b[j]}\\
max\{L(i-1,j),L(i,j-1)\}& \text{a[i]!=b[j]}
\end{cases}$$
为了简便，假设下标从1开始，那么边界条件：$L(0,j)=0,L(i,0)=0$。

时间和空间都是$O(mn)$。类似的，$L(i,j)$依赖于左上角$L(i-1,j-1)$、左边$L(i,j-1)$、上边$L(i-1,j)$，可以只存储上一行和当前行的$L$。进一步考虑：可以只存储当前行的$L$，外加一个变量$pre$存储左上角$L(i-1,j-1)$，空间可以优化到$O(min(m,n))$：

## Longest Increasing Subsequence
具体实现见Github

 - DP  
$dp[i]$表示从左向右扫描直到以a[i]元素结尾的序列所形成的LIS的长度，且子序列包含a[i]：
$$
dp[i]=max\{dp[i],1+dp[j]\}, 0\leq j<i,a[i]>a[j]
$$

最终答案即是$dp$数组的最大值：
时间$O(n^2)$，空间$O(n)$。

 - DP+Binary Search  
遍历数组的过程中，不停填充$dp$数组，维护$dp$数组使得其存储递增序列：  
如果$nums[i]>dp[-1]$，将$nums[i]$加入dp数组；  
否则，在dp数组中二分查找$nums[i]$的位置，并将`lower_bound`位置更新为`nums[i]`, 增大后续递增的可能性.  
举例来说，$nums=[0,8,4,12,2]$，$dp$数组：  
$[0]$  
$[0,8]$  
$[0,4]$  
$[0,4,12]$  
$[0,2,12]$  
虽然$dp$数组最终存储的不是LIS，但长度确是LIS的长度.
时间$O(nlogn)$，空间$O(n)$。

## Longest Palindromic Substring

 - Brute Force  
枚举每个子串的起始和结束位置，判断是否回文。时间$O(n^3)$，空间$O(1)$。
 - DP  
假设输入`ababa`，如果我们已经判断了`bab`是回文的，那么`ababa`就不需要再扫描一遍，因为两端都是`a`。
所以一个很直观的动规：
令$dp(i,j)$去**记忆**$i$和$j$之间的串是否回文，那么转移方程：
$$dp(i,j)=dp(i+1,j-1)\&\&s[i]=s[j]$$
边界条件$dp(i,i)=true,dp(i,i+1)=(s[i]=s[i+1])$：

```cpp
// 填充方向：由边界条件dp(i,i)向其他地方扩展，只需要填充j>i的三角形部分
class Solution {
public:
    string longestPalindrome(string s) {
        vector<vector<bool>> dp(s.length() + 1, vector<bool>(s.length() + 1, false));
        int start = 0, end = 0;
        int maxLen = 1;
        for (int i = 0; i < s.length(); ++i) {
            dp[i][i] = true;
            if (i < s.length() - 1) {
                if (s[i] == s[i + 1]) {
                    dp[i][i + 1] = true;
                    start = i;
                    end = i + 1;
                }
                else {
                    dp[i][i + 1] = false;
                }
            }
        }
        
        for (int i = s.length() - 1; i >= 0; --i) {
            for (int j = i + 2; j < s.length(); ++j) {
                if (s[i] == s[j]) {
                    dp[i][j] = dp[i + 1][j - 1];
                    if (dp[i][j] && maxLen < j - i + 1) {
                        start = i;
                        end = j;
                        maxLen = end - start + 1;
                    }
                }
                else {
                    dp[i][j] = false;
                }
            }
        }
        return s.substr(start, end - start + 1);
    }
};
```
时间$O(n^2)$，空间$O(n^2)$。
 - Expand Around Center  
回文串都是镜像对称的，可以遍历整个串，从当前位置向两边延伸，直到遇到不相等的字母。这里要考虑字符串长度的奇偶：

```cpp
class Solution {
public:
	string longestPalindrome(string s) {
		const int n = s.length();
		if (!n)
			return "";

		int start = 0, end = 0;
		for (int i = 0; i < n; ++i) {
			int len1 = expandCenter(s, i, i);
			int len2 = expandCenter(s, i, i + 1);
			int len = max(len1, len2);
			if (len > end - start + 1) {
				start = i - (len - 1) / 2;
				end = i + len / 2;
			}
		}
		return s.substr(start, end - start + 1);
	}
private:
	int expandCenter(string s, int start, int end) {
		while (start >= 0 && end < s.length() && s[start] == s[end]) {
			--start;
			++end;
		}
		return end - start - 1;
	}
};
```
时间$O(n^2)$，空间$O(1)$。

 - Manacher's Algorithm（待填）  
时间$O(n)$。

## Longest Palindromic Subsequence

 - DP  
令$dp(i,j)$表示介于$i$和$j$间的LPS的长度，那么状态转移方程：
$$dp(i,j)=
\begin{cases}
2+dp(i+1,j-1)& \text{s[i]=s[j]}\\
max\{dp(i+1,j),dp(i,j-1)\}& \text{s[i]$\neq$s[j]}
\end{cases}$$

```cpp
class Solution {
public:
    int longestPalindromeSubseq(string s) {
        const int n = s.size();
        if(!n) {
            return 0;
        }
        
        vector<vector<int>> dp(n, vector<int>(n, 0));
        int ans = 1;
        for(int i = n - 1;i >= 0;--i) {
            for(int j = i;j < n;++j) {
                if(i == j) {
                    dp[i][j] = 1;
                    continue;
                }
                if(s[i] == s[j]) {
                    dp[i][j] = dp[i + 1][j - 1] + 2;
                }
                else {
                    dp[i][j] = max(dp[i + 1][j], dp[i][j - 1]);
                }
                ans = max(ans, dp[i][j]);
            }
        }
        
        return ans;
    }
};
```
时间$O(n^2)$，空间$O(n^2)$。  
同样，空间可以优化到$O(n)$。

```cpp
class Solution {
public:
    int longestPalindromeSubseq(string s) {
        const int n = s.size();
        if(!n) {
            return 0;
        }
        
        vector<int> dp(n, 0);
        int ans = 1, pre = 0;
        for(int i = n - 1;i >= 0;--i) {
            pre = dp[0];
            for(int j = i;j < n;++j) {
                int tmp = dp[j];
                if(i == j) {
                    dp[j] = 1;
                    continue;
                }
                if(s[i] == s[j]) {
                    dp[j] = pre + 2;
                }
                else {
                    dp[j] = max(dp[j - 1], dp[j]);
                }
                pre = tmp;
                ans = max(ans, dp[j]);
            }
        }
        
        return ans;
    }
};
```