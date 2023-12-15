---
title: Minimum Spanning Tree
url: minimum-spanning-tree
date: 2020-01-26 15:34:00
description: 最小生成树
categories: Computer Science
tags: [Algorithm]
---

最小生成树算法用来在**无向带权图**中寻找一组边的集合, 该边集使得图中所有顶点连通, 没有环路并且权值和最小. 常见的MST算法包括Kruskal和Prim.

```cpp
struct Edge {
    int from, to, weight;
    bool operator == (const Edge& x) const {
        return weight == x.weight;
    }
};

struct EdgeHash {
    size_t operator () (const Edge& x) const {
        return x.from * 2 + x.to;
    }
};

unordered_set<Edge, EdgeHash> kruskalMST(int vertex_num, vector<Edge>& g) {
    unordered_set<Edge, EdgeHash> ans;
    std::sort(g.begin(), g.end(), [](const Edge& a, const Edge& b) {return a.weight < b.weight;});
    UnionFind uf(vertex_num);
    for (Edge& cur : g) {
        int u = uf.find(cur.from), v = uf.find(cur.to);
        if (u != v) {
            ans.insert(cur);
            uf.merge(u, v);
            if (ans.size() == vertex_num - 1) {
                break;
            }
        }
    }
    for (int i = 1; i < vertex_num; ++i) {
        if (uf.find(i) != uf.find(i + 1))
            cout << "不连通";
            return {};
    }
    return ans;
}

unordered_set<Edge, EdgeHash> primMST(vector<vector<int>>& g) {
    unordered_set<Edge, EdgeHash> ans;
    auto cmp = [](const Edge& a, const Edge& b) {
        return a.weight > b.weight;
    };
    // 解锁边进入小根堆
    priority_queue<Edge, vector<Edge>, decltype(cmp)> smallq(cmp);
    unordered_set<int> nodeset;

    int cnt = 0;
    nodeset.insert(0);
    for (int j = 1; j < g.size(); ++j) {
        if (g[0][j] != INT_MAX)
            smallq.push({0, j, g[0][j]});  // 解锁该点的边
    }
    while (!smallq.empty()) {
        Edge cur = smallq.top(); smallq.pop();
        int to_node = cur.to;  // 可能新点
        if (nodeset.find(to_node) == nodeset.end()) {
            ans.insert(cur);
            ++cnt;
            nodeset.insert(to_node);
            if (cnt == g.size() - 1) break;
            for (int j = 0; j < g.size(); ++j) {
                if (g[to_node][j] != INT_MAX && nodeset.find(j) == nodeset.end())
                    smallq.push({to_node, j, g[to_node][j]});
            }
        }
    }
    for (int i = 0; i < g.size(); ++i) {
        if (nodeset.find(i) == nodeset.end()) {
            cout << "不连通";
            return {};
        }
    }
    return ans;
}

typedef pair<int, int> p;
int primMST(vector<vector<p>>& g) {
    int ans = 0;
    unordered_set<int> vis;
    priority_queue<p, vector<p>, greater<p>> smallq;
    
    int cnt = 0;
    vis.insert(0);
    for (int i = 0; i < g[0].size(); ++i) {
        smallq.push(g[0][i]);
    }
    while (!smallq.empty()) {
        p cur = smallq.top(); smallq.pop();
        if (vis.find(cur.second) == vis.end()) {
            ans += cur.first;
            vis.insert(cur.first);
        }
        for (int i = 0; i < g[cur.second].size(); ++i) {
            if (vis.find(g[cur.second][i].second) == vis.end()) {
                smallq.push(g[cur.second][i]);
            }
        }
    }
    for (int i = 0; i < g.size(); ++i) {
        if (vis.find(i) == vis.end()) {
            cout << "不连通";
            return -1;
        }
    }
    return ans;
}

// 最小生成森林
unordered_set<Edge, EdgeHash> primMSForest(vector<vector<int>>& g) {
    auto cmp = [](const Edge& a, const Edge& b) {
        return a.weight > b.weight;
    };
    // 解锁边进入小根堆
    priority_queue<Edge, vector<Edge>, decltype(cmp)> smallq(cmp);
    unordered_set<int> nodeset;

    unordered_set<Edge, EdgeHash> ans;
    for (int i = 0; i < g.size(); ++i) {
        if (nodeset.find(i) == nodeset.end()) {
            nodeset.insert(i);
            for (int j = i + 1; j < g[i].size(); ++j) {
                if (g[i][j] != INT_MAX)
                    smallq.push({i, j, g[i][j]});  // 解锁该点的边
            }
            while (!smallq.empty()) {
                Edge cur = smallq.top(); smallq.pop();
                int to_node = cur.to;  // 可能新点
                if (nodeset.find(to_node) == nodeset.end()) {
                    ans.insert(cur);
                    nodeset.insert(to_node);
                    for (int j = 0; j < g.size(); ++j) {
                        if (g[to_node][j] != INT_MAX && nodeset.find(j) == nodeset.end())
                            smallq.push({to_node, j, g[to_node][j]});
                    }
                }
            }
        }
    }
    return ans;
}
```

## Kruskal  
将所有边的权值递增排序, 如果选择权值最小的边加入后不构成回路, 则将该边加入MST, 直到MST中有$n-1$条边或$n$个顶点.

那么如何判断加入边是否构成回路呢? 方法有很多啦, **并查集**是一个不错的选择. 每次加边时如果该边的两顶点$u$和$v$在同一个集合, 就判断下条边, 否则将该边加入答案并合并$u$和$v$.

遍历完成后, 所有顶点都同属一个集合, 因此可以通过判断顶点$i$和$i+1$是否同属一个集合, 进而判断该图是否连通.

用**边集**表示图比较方便, 复杂度$O(ElogE)$, 适合边稀疏而顶点相对多的图.

## Prim
Prim算法的思想是:

 1. 整个顶点集为$V$，初始选一个起点$s$，令集合$U=\{s\}, V=\{\}$;
 2. 在集合$U$与集合$V-U$中的点组成的边中，选一条权值最小的边$u_0v_0$加入MST，并且将$u_0$加入$U$;
 3. 重复直到MST有$n-1$条边或$n$个顶点为止.

因此, 需要一个`unordered_set`表示已经访问过的顶点集合$U$, 还需要一个`priority_queue`维护集合$U$与集合$V-U$之间的边权和dest.

用**邻接表**表示图比较方便, 复杂度$O(V^2)$, 适合边稠密的图.
