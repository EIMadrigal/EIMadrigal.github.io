---
title: 数据库 & 分布式 FAQ
url: db-and-distributed-faq
date: 2021-07-16 15:21:00
description: 可恶的八股
categories: Computer Science
tags: [Interview,System]
---

# 数据库
事务：一组逻辑操作的集合，满足ACID特性。事务要么全部成功，要么全部失败。

https://zhuanlan.zhihu.com/p/65281198
原子性：undo log记录事务修改前的数据信息，用来回滚
持久性：redo log记录已经成功提交的事务操作信息，用来恢复数据
隔离性：依靠读写锁和MVCC（多版本并发控制）实现。读写锁包括共享锁和排他锁，MVCC通过为数据添加时间戳实现。

数据库权限：GRANT REVOKE DENY

索引最左匹配原则 https://juejin.cn/post/6844903966690508814

关系型与非关系型 https://www.cnblogs.com/progor/p/8729798.html

连接器 分析器 优化器 执行器

数据库八股 https://zhuanlan.zhihu.com/p/403656116

## 分布式
云存储 https://www.zhihu.com/question/25834847/answer/348271275
云计算
云网络
云操作系统





















## 4 寻找两个正序数组的中位数
最直观的做法就是合并2个有序数组，根据奇偶返回中位数，时间空间复杂度均为$O(m+n)$。
```python
class Solution:
    def findMedianSortedArrays(self, nums1: List[int], nums2: List[int]) -> float:
        m, n = len(nums1), len(nums2)
        sortedArray = [0] * (m + n)
        i = j = k = 0
        while i < m and j < n:
            if nums1[i] < nums2[j]:
                sortedArray[k] = nums1[i]
                i += 1
            else:
                sortedArray[k] = nums2[j]
                j += 1
            k += 1
        while i < m:
            sortedArray[k] = nums1[i]
            k += 1
            i += 1
        while j < n:
            sortedArray[k] = nums2[j]
            k += 1
            j += 1
        if (m + n) % 2:
            return sortedArray[(m + n) // 2]
        else:
            return (sortedArray[(m + n) // 2 - 1] + sortedArray[(m + n) // 2]) / 2
```

空间的改进可以避免开辟新数组而采用2个指针，时间$O(m+n)$，空间$O(1)$：
```python
class Solution:
    def findMedianSortedArrays(self, nums1: List[int], nums2: List[int]) -> float:
        m, n = len(nums1), len(nums2)
        i = j = cnt = 0
        left = right = 0
        while cnt <= (m + n) // 2:
            left = right
            if i < m and j < n:
                if nums1[i] < nums2[j]:
                    right = nums1[i]
                    i += 1
                else:
                    right = nums2[j]
                    j += 1
            elif i == m:
                right = nums2[j]
                j += 1
            else:
                right = nums1[i]
                i += 1
            cnt += 1
        if (m + n) % 2:
            return right
        else:
            return (left + right) / 2
```

要做到log级别的复杂度，需要使用[二分法](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-w-2/)。

## 10 正则表达式匹配

判断字符串`s`是否匹配正则表达式`p`，`p`仅包含`.`和`*`。`.`匹配任意单个字符，`*`匹配0或多个前一个字符。题目保证不会出现连续的`*`。

可以发现此题的难点在于`*`，`a*`可以表示`"", a, aa, aaa`，`.*`可以表示`"", ., .., ... `。第一次看到这个题不一定能想到DP，甚至暴力做法也不知道从哪入手。我认为核心在于先不要考虑`*`到底需要匹配几次，而是考虑`*`到底是否需要匹配，即匹配0次/匹配1次的问题，多次匹配不过是将匹配1次重复而已。

例如`s=aa,p=a*`，从匹配0次/匹配1次的角度出发，暴搜的过程可以表示为一棵二叉树，左孩子表示匹配1次，右孩子表示匹配0次，开始时指针位置`(0,0)`，此时我们发现`s[i] == p[j] && p[j + 1] == *`，意味着可以选择匹配0次或1次a：如果0次，意味着放弃目前遇到的这个`*`，那么只能`i不变, j = j + 2`，j越界匹配结束返回False；如果1次，意味着`s[i]`匹配成功，那么可以继续使用目前的这个`*`匹配`s[i + 1], j不变`。直到将`s`的所有字符匹配完毕，时间复杂度$O(2^n)$。

另外一个例子`s=aab,p=c*a*b`，从`(i,j)`开始，先来看看可能使得匹配成功的情形：

- `p[j + 1] == *`：无论当前字符是否匹配都要继续，因为`*`可以匹配0次，保留了成功的可能。此时可以选择匹配0次/匹配1次，如果选择匹配0次，意味着抛弃目前的`*`转移到`(i,j+2)`；如果选择匹配1次，意味着当前字符必须匹配且转移到`(i+1,j)`。

- 如果`p[j + 1] != *`，那么当前字符必须匹配即`s[i] == p[j] || p[j] == .`，否则无法继续进行返回False。如果当前匹配，那么继续判断`(i+1,j+1)`。

如果上述2种成功的条件均不满足，那么只能返回False。

终止条件则是：

- `i >= len(s) && j >= len(p)`意味着字符串的所有字符匹配整个模式，返回True
- `i is ok && j >= len(p) `意味着`s`中仍然存在未被匹配的字符但模式已经耗尽，返回False
- `i >= len(s) && j is ok`意味着`s`中所有字符均匹配完毕但是模式仍未耗尽，此时不能判断成功与否，需要继续递归。

即递归函数`dfs(i,j)`返回`s[i:]`与`p[j:]`是否完全匹配，暴力做法存在着很多重复计算，因此用哈希表存储已经求得的结果提高效率。

[Top-Down Memoization - Leetcode 10](https://www.youtube.com/watch?v=HAA8mgxlov8)讲得比较透彻。

## 2141 同时运行N台电脑的最长时间

有n台电脑和一个电池数组`batteries`，第i个电池可以让一台电脑运行`batteries[i]`分钟，求n台电脑同时运行多久。

我第一次看到的时候就想到了优先队列，时间最久即每次选n个最大的电池`a=[]`，然后可以支撑`min(a)`分钟，直到`batteries`中正数个数小于n停止。

但发现连样例都过不了，比如`n=2,batteries=[3,3,3]`，第一次选2个最大的支撑3min，`batteries=[0,0,3]`，无法同时继续，但答案是错的。贪心策略不太对，每次选最大的n个是对的，但是不一定要支撑`min(a)`分钟。

最后发现二分答案是反着思考的：虽然求的是最长时间，先假设n台电脑可以同时运行t分钟，一台电池最多能供电t分钟，所有电池的可供电时间总和为S = min(sum(batteries), t*len(batteries))，检查这些时间是否能给n台电脑供电S / t >= n

[这张图很形象](https://leetcode-cn.com/problems/maximum-running-time-of-n-computers/solution/er-fen-da-an-de-checkhan-shu-de-si-kao-f-g8no/)
对于当前枚举的时间P，共有k台电脑。需要看电池能否填满该矩阵，同一行不能有相同颜色。

对于大于等于P的电池，只能利用P，让其一直供应一台电脑即可，即填充一列，剩余电量只能丢弃，因为该电池不能同时供应2台电脑。对于小于P的，让其供应一台电脑直到电量耗尽，注意这些小于P的电池不可能出现同时供应2台电脑的情况，对于小于P的电池，首先拿一个填充一列，当然填不满，接着拿第二个继续填充，如果该列满了就填下一列，以此类推，由于小于P的电池最多填充P - 1行，因此不会出现相同颜色。（最极端的情况就是：某种颜色的电量为1，只能填1行，第二种颜色最多P-1行，因此下一列不会重复，如果第二种颜色可以填P行，那么下一列才会重复）。

假设共m个电池, 大于等于P的有a个, 小于P的有m-a个. 由于大于等于P的只需要撑a台电脑, 因此问题转变为小于P的m-a个电池能否撑n-a台, 即sum(m-a) >= (n-a) * P. 稍微转化下: sum(m-a) + a * P >= n * P.

因此判断P是否合法只需要求所有电池里：如果电量大于等于P，sum += P，否则将剩余所有的电量小于P的电量全部加到sum起来，判断sum是否大于等于P * K。

二分法一般的题目就是求最大的最小或最小的最大，假设要求的是ans，那么从ans=0开始判断是否满足要求，然后判断ans=1,2,...直到上界。例如本题就是求最小里面的最大，比如0,1,2,...都满足要求，需要在其中挑一个最大的。

## 767
给定字符串，重排其中的字符使得任意两个相邻位置的字母不同。
样例：s="aab"，输出"aba"
最开始的想法是贪心+双指针，指针i从前向后遍历，指针j从i+1开始，如果s[i]==s[j]，j不断向后遍历找到第一个与s[i]不同的字母s[k]，将s[k]与s[j]交换。WA在了"baaba"，期待"ababa"，输出""，所以这种贪心策略显然是错的。

接着就想到需要考虑字符的出现频率，先按频率高低排序再去按照上述贪心，WA在了"aabbcc"，期待"abacbc"，输出""，很显然这种贪心策略本身就是错的。

接着就想到先安排出现次数最多的，如果当前要安排的与前一个字符相同，就选择出现次数第二多的，这样交替下去，可以写出如下代码：
```
class Solution:
    def reorganizeString(self, s: str) -> str:
        if len(s) == 1:
            return s

        from collections import Counter
        dic = Counter(s)

        ans = ""
        prev = ""
        for i in range(0, len(s)):
            cnts = list(dic.items())
            cnts = sorted(cnts, key=lambda x:x[1], reverse=True)
            if prev == cnts[0][0]:
                if len(cnts) <= 1 or cnts[1][1] == 0:
                    return ""
                ans += cnts[1][0]
                dic[cnts[1][0]] -= 1
                prev = cnts[1][0]
            else:
                ans += cnts[0][0]
                dic[cnts[0][0]] -= 1
                prev = cnts[0][0]
        return ans

```
时间复杂度$O(n^2lgn)$，空间$O(n)$。

因为涉及到出现次数最多的问题，可以考虑max heap。每次迭代时，从堆里弹出堆顶元素（意味着下次迭代该元素不会考虑）加入res，如果上一次迭代加入res的元素还有剩余，就将其重新加入heap，意味着下次迭代需要考虑该元素。
```
class Solution:
    def reorganizeString(self, s: str) -> str:
        ans, cnt = [], Counter(s)  # decent order
        max_heap = [(-value, key) for key, value in cnt.items()]
        heapq.heapify(max_heap)
        prev_ch, prev_cnt = '', 0
        while max_heap:
            cnt, ch = heapq.heappop(max_heap)
            ans.append(ch)
            if prev_cnt < 0:
                heapq.heappush(max_heap, (prev_cnt, prev_ch))
            cnt += 1
            prev_ch, prev_cnt = ch, cnt
        if len(s) != len(ans):
            return ""
        return ''.join(ans)
```
时间复杂度$O(n)$，空间$O(n)$。

[题目链接](https://leetcode.com/problems/remove-zero-sum-consecutive-nodes-from-linked-list/)

## 题意

给定一个单链表，删除和为0的连续结点序列，直到最终链表中没有和为0的连续结点序列。
样例：head = [1,2,3,-3,-2]，输出[1]

## 分析

由于链表头结点可能会被删除，因此首先创建dummy结点。一个比较直观的想法就是记录前缀和，依次遍历链表，遇到出现过的前缀和也就意味着出现了和为0的序列，就删除两次相同前缀和中间的序列。因此需要一个hashtable记录前缀和出现的位置，手动走一个简单样例吧：
dummy设为`(0,head)`，hashtable初始包含`{0:dummy}`，避免[1,-1]这种情况。

  1. `cur=p(1),sum=1,hash={0:dummy,1:p(1)}`
  2. `cur=p(2),sum=3,hash={0:dummy,1:p(1),3:p(2)}`
  3. `cur=p(3),sum=6,hash={0:dummy,1:p(1),3:p(2),6:p(3)}`
  4. `cur=p(-3),sum=3`：此时hash返回p(2)，因此就让p(2).next指向cur.next，相当于删除了[3,-3]，`hash={0:dummy,1:p(1),3:p(2),6:p(3)}`
  5. `cur=p(-2),sum=1`：此时hash返回p(1)，因此让p(1).next指向cur.next，相当于删除了[2,-2]，`hash={0:dummy,1:p(1),3:p(2),6:p(3)}`

大概可以写出这样的代码：

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def removeZeroSumSublists(self, head: ListNode) -> ListNode:
        dummy = ListNode(next=head)
        cur = head
        prefix_sum = 0
        hashtable = {0:dummy}
        while cur:
            prefix_sum += cur.val
            p = hashtable.get(prefix_sum)
            if p:
                p.next = cur.next
            else:
                hashtable[prefix_sum] = cur
            cur = cur.next
        return dummy.next
```

交上去WA了，问题出在哪呢？
走一遍出错的样例：输入[1,3,2,-3,-2,5,5,-5,1]，期待[1,5,1]，输出[1,5,5,-5,1]

  1.  `cur=p(1),sum=1,hash={0:dummy,1:p(1)}`
  2.  `cur=p(3),sum=4,hash={0:dummy,1:p(1),4:p(3)}`
  3.  `cur=p(2),sum=6,hash={0:dummy,1:p(1),4:p(3),6:p(2)}`
  4.  `cur=p(-3),sum=3,hash={0:dummy,1:p(1),4:p(3),6:p(2),3:p(-3)}`
  5.  `cur=p(-2),sum=1`：此时hash返回p(1)，让p(1).next指向p(-2).next，链表变为了[1,5,5,-5,1]，`hash={0:dummy,1:p(1),4:p(3),6:p(2),3:p(-3)}`
  6.  `cur=p(5),sum=6`：此时hash返回p(2)，但此时p(2)已经删除，因此让p(2).next指向p(5).next肯定是错的。

至此应该可以看出问题了：在删除改变链表指针的同时，hashtable并没有做相应的同步删掉对应的元素，所以每当出现重复前缀和时只要删掉hashtable中两次前缀和之间的项即可，可以借助`OrderedDict()`实现，按照插入顺序即链表顺序有序：

  1.  `cur=p(-3),sum=3,hash={0:dummy,1:p(1),4:p(3),6:p(2),3:p(-3)}`
  2.  `cur=p(-2),sum=1`：此时hash(1)返回p(1)，删除后为`hash={0:dummy,1:p(1)`
  3.  `cur=p(5),sum=6`：此时hash返回`None`，符合预期，继续迭代即可。

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def removeZeroSumSublists(self, head: ListNode) -> ListNode:
        dummy = ListNode(next=head)
        cur = head
        prefix_sum = 0
        hashtable = OrderedDict({0:dummy})
        while cur:
            prefix_sum += cur.val
            prev = hashtable.get(prefix_sum, cur)
            while prefix_sum in hashtable:
                hashtable.popitem()
            hashtable[prefix_sum] = prev
            prev.next = cur = cur.next
        return dummy.next
```

Two-pass：
上述做法的hashtable记录的是第一次出现前缀和的位置。现在换一种思路：首先遍历一次链表，hashtable记录**最后一次**出现前缀和的位置，第二次遍历链表设置相应的指针到最后一次前缀和的位置，这样即使第一次前缀和位置被删除，指针也会相应地跳过：

  1. 第一次遍历后：`hash={0:dummy,1:p(-2),4:p(3),6:p(-5),3:p(-3),11:p(5),7:p(1)}`
  2. 第二次遍历：`dummy.next=hash[0].next,p(1).next=hash[1].next=p(5),p(5).next=hash[6].next=p(1),p(1).next=hash[7].next=null`

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def removeZeroSumSublists(self, head: ListNode) -> ListNode:
        dummy = ListNode(0, head)
        prefix_sum = 0
        hashtable = {0:dummy}
        while head:
            prefix_sum += head.val
            hashtable[prefix_sum] = head
            head = head.next
            
        prefix_sum = 0
        head = dummy
        while head:
            prefix_sum += head.val
            head.next = hashtable[prefix_sum].next
            head = head.next
        return dummy.next
```

时间复杂度$O(n)$，空间复杂度$O(n)$。