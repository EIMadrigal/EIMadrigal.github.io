---
title: Union-Find
url: union-find
date: 2020-01-26 17:50:00
description: 并查集
categories: Computer Science
tags: [Algorithm]
---

## Motivation
并查集是一种用来维护集合的数据结构，底层通过`parent`数组实现，每个集合只有唯一的根结点，并将其作为该集合的标志。

并查集有两个基本操作：
  
 1. 并：合并两集合；
 2. 查：查询某元素的父结点。

初始化所有元素都是一个独立的集合：
```cpp
int parent[MAXN];
for (int i = 0; i <= n; i++) {
    parent[i] = i;    // i元素的父结点初始化为自己，也可以初始化为-1
}
```

## 合并与查找

 - Find  
查找某个元素属于哪个集合（如果单独成集就返回自己），返回该集合的代表元素（即parent为-1的那个元素/parent为自身的那个元素）。  
查找的同时可以通过**路径压缩**来将均摊复杂度降低为$O(1)$。查找某个结点时，将其经过的全部结点直接连到根结点，这样下次的查询次数就会急剧减小。
 - Union  
合并时可以遵循**按秩合并**原则，将秩小的树合并到秩大的树，降低合并时路径压缩的开销。  
将两个不同的集合合并，只要将其中一个集合的根结点的`parent`指向另一个集合的根结点即可。  
对于属于同一个集合的两个元素的合并没有意义，所以我们一般只对两个不同的集合进行合并，这样避免同一个集合成环，因此并查集的每个集合都是一棵树。

```cpp
class UnionFind {
private:
    vector<int> parents_;
    vector<int> ranks_;
    int setsCnt;

public:
    UnionFind(int n) : setsCnt(n) {
        // 是否等于n取决于index从0开始还是从1开始
        for(int i = 0; i <= n; ++i) {
            parents_.emplace_back(i);
            ranks_.emplace_back(0);
        }
    }

    // get the root of x
    int find(int x) {
        // path compression
        if (x != parents_[x]) {
            parents_[x] = find(parents_[x]);
        }
        return parents_[x];
    }

    // merge set u and v
    // false -> u and v are already in one set
    bool merge(int u, int v) {
        int rootu = find(u);
        int rootv = find(v);
        if (rootu == rootv)
            return false;

        // merge low rank to high rank
        if (ranks_[rootu] < ranks_[rootv]) {
            parents_[rootu] = rootv;
        }
        else if (ranks_[rootu] > ranks_[rootv]) {
            parents_[rootv] = rootu;
        }
        else {
            parents_[rootu] = rootv;
            ++ranks_[rootv];
        }
        setsCnt--;
        return true;
    }

    int getSetCnt() {
        return setsCnt;
    }
};
```

并查集可以将均摊复杂度变为$O(1)$，但如果涉及到删除元素、计算每个集合元素个数等操作时，实现会有些复杂；  
好吧！计算每个集合元素个数以及统计有多少个集合并不复杂，计算元素个数可以开一个数组cnt初始全为1，每次merge都更新cnt即可，cnt[i]即为根为i的集合包含的元素个数  
统计多少个集合遍历所有元素，遇到不同的根结点就更新ans。

[最原始的并查集](https://www.zxpblog.cn/2020/02/17/%E9%AB%98%E7%BA%A7%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%EF%BC%9A%E5%AD%97%E5%85%B8%E6%A0%91%E5%92%8C%E5%B9%B6%E6%9F%A5%E9%9B%86%E5%92%8C%E7%BA%BF%E6%AE%B5%E6%A0%91/)虽然复杂度稍差，但是可以完成的功能比较多。  
[原始并查集实现](https://www.cnblogs.com/EIMadrigal/p/12693959.html)

一道例题：[亲戚](https://www.luogu.com.cn/problem/P1551)  
不做路径压缩可以求[最小环长](https://www.nowcoder.com/profile/135924065/codeBookDetail?submissionId=83987857)

## 无向图判环
并查集可以用来检测无向图是否有环.  
初始时认为每个节点都是独立的集合, 如果在加入边$(i,j)$之前节点$i$和节点$j$已经同属一个集合, 意味着节点$i$和节点$j$必然有某条其他路径连通, 该路径和边$(i,j)$必然构成环.
```cpp
vector<pair<int, int>> g;  // edge list
g.push_back(make_pair(0, 1));
g.push_back(make_pair(0, 2));
g.push_back(make_pair(1, 2));

bool hasCycle(vector<pair<int, int>>& g) {
	for (int i = 0; i < g.size(); ++i) {
		int rootx = Find(g[i].first);
		int rooty = Find(g[i].second);
		if (rootx == rooty) {
			return true;
		}
		Union(rootx, rooty);
	}
	return false;
}
```

## 种类并查集（TODO）
普通并查集的特点就是只有一个集合，比如上述例题只有亲戚一个集合。如果涉及到多个集合，就需要种类并查集。  
假如有n个集合，常用的手法就是开一个n倍大小的并查集。  
[食物链](https://www.luogu.com.cn/problem/P2024)  
[团伙](https://www.luogu.com.cn/problem/P1892)
