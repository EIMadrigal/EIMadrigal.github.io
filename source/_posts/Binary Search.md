---
title: Binary Search
url: binary-search
date: 2020-01-11 15:17:00
description: 二分查找
categories: Computer Science
tags: [Algorithm]
---

## 基本型
对于一个**有序**数组，查找某个元素，存在返回其index，否则返回-1。  
二分查找有4个地方容易混淆：

 1. `l`和`r`的初始化，即区间定义。可以是$[l,r]$，也可以是$[l,r)$
 2. `while`的循环条件：可以是`<`/`<=`
 3. `l`和`r`的更新：可以是`mid`/`mid+1`/`mid-1`
 4. 返回值：可以是`l`/`r`/`mid`/其他值

如果区间初始化为左闭右闭，循环条件就应该是小于等于，因为等于时待检查区间还有1个元素，需要继续；并且r的更新应该是mid-1，因为已经确定mid不是target且右边界是闭的。

```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        l, r = 0, len(nums) - 1
        while l <= r:
            m = l + (r - l) // 2
            if nums[m] == target:
                return m
            elif nums[m] > target:
                r = m - 1
            else:
                l = m + 1
        return -1
```

如果区间定义为左闭右开，此时循环条件小于，等于时表示区间为空；当mid>target时r应该为mid，因为mid不是target且右边界开，即下一次不会考虑mid。这种情况如果没找到最终必然有l==r

我最常用的板子是左闭右开`[l, r)`，因为这种方式保留了所有的可能性，比如在l位置插入或者在r位置插入，保留了r位置成为答案的可能。

一些隐晦的问题都隐藏着**单调性**，需要发掘最大值最小化/最小值最大化，或者说当你按顺序尝试每个可能的解并且要在其中找到最小或最大的时候，就考虑二分。

```cpp
int binarySearch(vector<int>& nums, int target) {
    int l = 0, r = nums.size();
    while (l < r) {
        int m = l + (r - l) / 2;
        if (target == nums[m]) return m;
        if (target > nums[m]) l = m + 1;
        else r = m;
    }
    return -1;  // not found
}
```

稍微扩展一些的题目有`lower_bound()`和`upper_bound()`:  
对于`lower_bound()`，即查找满足$x\geq target$的最小index：
```cpp
int lower_bound(vector<int>& nums, int target) {
    int l = 0, r = nums.size();
    while (l < r) {
        int m = l + (r - l) / 2;
        if (target > nums[m]) l = m + 1;
        else r = m;
    }
    return l;
}
```
可变形为查找最后一个小于$target$的数：即$l-1$。  
对于`upper_bound()`，即查找满足$x>target$的最小$x$的index：
```cpp
int upper_bound(vector<int>& nums, int target) {
    int l = 0, r = nums.size();
    while (l < r) {
        int m = l + (r - l) / 2;
        if (target >= nums[m]) l = m + 1;
        else r = m;
    }
    return l;
}
```
可变形为查找最后一个小于等于$target$的数：即$l-1$。


Leetcode 704/35/34/69/367

## [Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/)
Find the index of the target if it is in the array, else return -1. All values of the array are **unique**.

We can find that at least half of the elements are sorted, so we should find out whether it's on the left or the right.
 
Let's see an example: [0,1,2,3]{all sorted}, [3,0,1,2]{right}, [2,3,0,1]{left or right}, [1,2,3,0]{left}.  
Another example: [0,1,2,3,4]{all sorted}, [4,0,1,2,3]{right}, [3,4,0,1,2]{right}, [2,3,4,0,1]{left}, [1,2,3,4,0]{left}. It is important to know that the sorted side is **at least half of the array** (longer). So the mid is in this side.

We can compare nums[mid] with nums[left] (or nums[right]) to decide which side is sorted. If `nums[mid]>nums[left]`, then left half is sorted, else right is sorted.

The second step is to compare the target with nums[mid] to narrow down the range.

 - Left half is sorted.  
If target>nums[mid], it must lie in the right interval. So we can make left pointer forward left = mid + 1.  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210303220721888.png)  
If target<nums[mid], there exists 2 situations:  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210303214903684.png)  
The first graph means target is less than mid but bigger than or equal to left, we move the right pointer toward left right = mid.  
The second graph means target is less than left, thus we move the left pointer towards right left = mid + 1.

Finally if nums[mid]==nums[left], it means that left and mid are pointing to the same element due to the distinct values. In this case right must be mid or mid + 1. Where is the target? It must be on the right side of mid (actually nums[right]) or it do not exist. So we can move the left pointer by 1 to see nums[right] is equal to target or not. 

 - Right half is sorted.
You can analysis this by yourself.

We can write the following code based on the previous discussion:

```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        l, r = 0, len(nums) - 1
        while l <= r:
            m = l + (r - l) // 2
            if target == nums[m]:
                return m
            if nums[m] > nums[l]: # left side is sorted
                if target > nums[m]:
                    l = m + 1
                else:
                    if target >= nums[l]:
                        r = m - 1
                    else:
                        l = m + 1
            elif nums[m] < nums[l]: # right side is sorted
                if target < nums[m]:
                    r = m - 1
                else:
                    if target <= nums[r]:
                        l = m + 1
                    else:
                        r = m - 1
            else:
                l += 1
        return -1
```

## [Rotated Sorted Array II](https://leetcode.com/problems/search-in-rotated-sorted-array-ii/)
It is the same as the last one except that the array may contains **duplicates**. And you do not need to find the index but return true or false.

The code is the same except that you should return T or F. 

[Find Minimum in Rotated Sorted Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/) shares the same idea:

```python
class Solution:
    def findMin(self, nums: List[int]) -> int:
        l, r = 0, len(nums) - 1
        ans = float('inf')
        while l <= r:
            m = (l + r) >> 1
            ans = min(ans, nums[m])
            if nums[m] > nums[l]:
                ans = min(ans, nums[l])
                l = m + 1
            elif nums[m] < nums[l]:
                ans = min(ans, nums[m])
                r = m - 1
            else:
                l += 1
        return ans
```

## [Rotated Sorted Array yyds](https://leetcode-cn.com/problems/search-rotate-array-lcci/)
In this case you should return the index of the target in a duplicated array. If you do not know the idea above, this will be a little harder to solve since there are many corner cases.

```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        l, r = 0, len(nums) - 1
        ans = float('inf')
        while l <= r:
            m = l + (r - l) // 2
            if target == nums[m]:
                ans = min(ans, m)
                r = m - 1 # do not return since there might be smaller index
                continue
            if nums[m] > nums[l]: # left side is sorted
                if target > nums[m]:
                    l = m + 1
                else:
                    if target >= nums[l]:
                        r = m - 1
                    else:
                        l = m + 1
            elif nums[m] < nums[l]: # right side is sorted
                if target < nums[m]:
                    r = m - 1
                else:
                    if target < nums[r]:
                        l = m + 1
                    elif target > nums[r]:
                        r = m - 1
                    else:
                        if target == nums[l]:
                            ans = min(ans, l)
                            break
                        else:
                            l = m + 1
            else:
                l += 1
        return -1 if ans == float('inf') else ans
```