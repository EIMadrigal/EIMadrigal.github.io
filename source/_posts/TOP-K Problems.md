---
title: TOP-K Problems
url: top-k-problems
date: 2020-03-31 11:01:00
description: TOP-K问题
categories: Computer Science
tags: [Algorithm]
---

## 最小的K个数

 - 直接数组排序，取出前K个。复杂度$O(nlogn)$。
 - 分治  
 此题只要求出最小的K个数，并不要求这K个数有序。  
 我们可以借鉴快排中的`partition`做法，将比第K个数小的都放前面，其余都放后面，即得到答案，但是这种方法会**改变原有数组**：

```cpp
class Solution {
public:
	vector<int> topKMin(vector<int>& nums, int k) {
		if (k < 1 || k > nums.size()) {
			return {};
		}
		
		int start = 0, end = nums.size() - 1;
		int index = partition(nums, start, end);
		while (index != k - 1) {
			if (index > k - 1) {
				end = index - 1;
				index = partition(nums, start, end);
			}
			else {
				start = index + 1;
				index = partition(nums, start, end);
			}
		}

		return vector<int>(begin(nums), begin(nums) + k);
	}
private:
	int partition(vector<int>& nums, int l, int r) {
		if(nums.empty() || l < 0 || r >= nums.size())
			return -1;
			
		int pivotIndex = randomNum(l, r);
		swap(nums[pivotIndex], nums[r]);

		int smaller = l - 1;
		for (int i = l; i < r; ++i) {
			if (nums[i] <= nums[r]) {
				++smaller;
				swap(nums[smaller], nums[i]);
			}
		}
		++smaller;
		swap(nums[smaller], nums[r]);
		return smaller;
	}

	int randomNum(int x, int y) {
		srand(time(0));  // use system time as seed
		return x + rand() % (y - x + 1);
	}
};
```
可以得到递归关系：$T(n)=T(n/2)+n$，由主定理可知复杂度$O(n)$。  
与快排不同的是：快排要处理2个子问题，故为$T(n)=2T(n/2)+n$，复杂度$O(nlogn)$。  
关于复杂度，还可以用代入法证明：
$$T(n)=T(n/2)+n=T(n/4)+n/2+n=T(n/8)+n/4+n/2+n=...$$
重复k次后：
$$T(n)=T(n/2^k)+n/2^{k-1}+...+n/2+n$$
故：$T(n)=n+n/2+n/4+...+1=2n+1$

 - 堆/红黑树  
主要思路是用容器存储K个数，之后不断更新：如果当前值小于容器最大值，替换最大值。  
用最大堆作为容器，删除及插入$O(lgk)$，故总复杂度$O(nlgk)$：

```cpp
// max heap
class Solution {
public:
	priority_queue<int> topKMin(vector<int>& nums, int k) {
		if (k < 1 || k > nums.size()) {
			return {};
		}
		
		priority_queue<int> q;
		for (vector<int>::iterator it = nums.begin(); it != nums.end(); ++it) {
			if (q.size() < k) {
				q.push(*it);
			}
			else {
				if (q.top() > * it) {
					q.pop();
					q.push(*it);
				}
			}
		}
		return q;
	}
};
```
当然也可以使用红黑树：

```cpp
// multiset
class Solution {
public:
	vector<int> topKMin(vector<int>& nums, int k) {
		if (k < 1 || k > nums.size()) {
			return {};
		}

		multiset<int, greater<int>> ms;
		for (vector<int>::iterator it = nums.begin(); it != nums.end(); ++it) {
			if (ms.size() < k) {
				ms.insert(*it);
			}
			else {
				if (*ms.begin() > * it) {
					ms.erase(ms.begin());
					ms.insert(*it);
				}
			}
		}
		return vector<int>(ms.begin(), ms.end());
	}
};
```
之所以说这种解法适用于海量数据，是因为很多时候不能一次性把数据读入内存处理，这种解法可以从硬盘一次读一个，判断是否放入容器即可，只需要在内存中存储容器即可。

## 最常出现的K个数

 - 统计出现频率，按频率排序后取出前K个。复杂度$O(nlgn)$。
 - 小根堆。维护K个数，如果新数的频率大于堆顶，替换之。复杂度$O(nlgk)$。

```cpp
class Solution {
public:
	vector<int> topKFrequent(vector<int>& nums, int k) {
		vector<int> ans;
		unordered_map<int, int> cnt;

		for (int i = 0; i < nums.size(); ++i) {
			++cnt[nums[i]];
		}

		priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> q;
		for (auto p : cnt) {
			q.emplace(p.second, p.first);
			if (q.size() > k) {
				q.pop();
			}
		}

		for (int i = 0; i < k;++i) {
			ans.push_back(q.top().second);
			q.pop();
		}
		return ans;
	}
};
```

 - 快速选择。

 - 桶排。用很多桶记录不同频率到对应数字的映射。时间$O(n)$，空间$O(n)$。

```cpp
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        vector<int> ans;
        unordered_map<int, int> cnt;
        
        int maxFre = 0;
        for(const int i : nums) {
            maxFre = max(maxFre, ++cnt[i]);
        }
        
        unordered_map<int, vector<int>> bucket;  // freq -> nums
        for(const auto& p : cnt) {
            bucket[p.second].push_back(p.first);
        }
        
        for(int i = maxFre;i > 0;--i) {
            for(int a : bucket[i]) {
                ans.push_back(a);
                if(ans.size() == k) {
                    return ans;
                }
            }
        }
        
        return ans;
    }
};
```