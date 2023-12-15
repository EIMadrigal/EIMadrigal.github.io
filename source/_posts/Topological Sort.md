---
title: Topological Sort
url: topological-sort
date: 2019-06-01 12:11:00
description: 拓扑排序
categories: Computer Science
tags: [Algorithm]
---

## 拓扑排序

拓扑排序将有向无环图(DAG)的所有顶点排成一个线性序列，满足

 - 每个结点只出现一次
 - 任意两个顶点若存在有向边$u\rightarrow v$，那么在线性序列中$u$必然在$v$之前

非DAG图是不存在拓扑序列的.

拓扑排序的实现有BFS和DFS的方式，BFS的思想是：

 1. 将所有入度为0的顶点入队；
 2. 取队首结点输出，删除所有从该结点出发的边，并将这些边到达的顶点的入度减1，若某顶点入度减为0，将其入队；
 3. 重复2，直到队列为空。若进过队的结点数为$n$，排序成功，否则**图中有环**。

时间复杂度$O(V+E)$，如果需要按字典序输出，就用优先队列。

dfs version, reverse ans is the topological sequence

```cpp
// BFS + 邻接表
bool topologicalSort(vector<vector<int>>& g, vector<int>& indegree) {
    int n = g.size(), cnt = 0;
    queue<int> q;  // 若有多个入度为0的顶点要选择编号最小的，可使用priority_queue
    for (int i = 0; i < n; ++i) {
        if (indegree[i] == 0)
            q.push(i);
    }

    while (!q.empty()) {
        int cur = q.front(); q.pop();
        ++cnt;
        cout << cur << endl;  // 输出拓扑序列
        for (auto nei : g[cur]) {
            if (--indegree[nei] == 0)
                q.push(nei);
        }
    }
    return cnt == n;  // 排序成功, 否则有环
}

// 三色DFS + 邻接表
bool topologicalSort(vector<vector<int>>& g) {
    int n = g.size();
    // 0 -> 未访问, 2 -> 已访问, 但不是当前dfs访问的
    // 1 -> 已访问且是当前dfs访问的(u->v,v->t,t->u), 有环
    vector<int> vis(n, 0);
    for (int i = 0; i < n; ++i) {
        if (vis[i] == 0)
            if (dfs(g, vis, i))
                return false;  // 有环
    }
    return true;
}

// 判断从u可达的图是否有环
bool dfs(vector<vector<int>>& g, vector<int>& vis, int u) {
    vis[u] = 1;
    for (auto nei : g[u]) {
        if (vis[nei] == 0) {
            if (dfs(g, vis, nei)) return true;
        } else if (vis[nei] == 1) {
            return true;
        }
    }
    vis[u] = 2;
    cout << u << "\n";  // 输出拓扑序列逆序
    return false;
}
```

## 图论判环

### 无向图判环

1. DFS + parent node

这里不要求无向图连通，如果无向图中的任一连通分量中有环，就说无向图中存在环。

对每个未访问过的顶点进行DFS，会在该顶点所处的连通分量上产生一棵DFS tree，图中存在环当且仅当树上存在back edge，这条回边要么关联自己成为自环边（简单图不允许自环），要么关联树上该顶点的某个祖先结点。因此，如果有一条指向已经访问过的顶点的回边，就返回`true`。具体来说，对于所有邻接点，如果某个邻接点不是父结点但却被访问过，证明有环。

对于一条边关联的2个顶点，DFS时从u到邻接点v，u在树上是v的父结点，那么对v访问邻接点时需要特判u。时间复杂度$O(V+E)$，空间复杂度$O(V)$。

```cpp
vector<vector<int>> g(n);
vector<bool> vis(n, false);

// 判断从u可达的连通分量是否有环
bool dfs(int u, int parent) {
    vis[u] = true;
    for (int nei : g[u]) {
        if (!vis[nei]) {
            if (dfs(nei, u)) return true;
        } else if (nei != parent) { // 该邻接点已访问过且不是u的父结点, 有环
            return true;
        }
    }
    return false;
}

bool hasCycle() {
    for (int i = 0; i < n; ++i) {
        if (!vis[i]) {
            if (dfs(i, -1)) {
                return true;
            }
        }
    }
    return false;
}
```

DFS如果需要输出环路，需要在`dfs`里添加一个`parent`数组，记录顶点在DFS tree上的父结点。

2. BFS

BFS的基本思想与DFS类似：对于当前访问的结点cur，将其标记，访问cur的所有邻接点adj，如果adj已经被标记为访问过但却不是cur的父结点，表明有环。

不过，BFS需要一个`parent`数组记录每个顶点的父结点，区分邻接点中**环中的顶点**和**遍历中的父结点**（只用`vis`数组无法区分），这样才不会错误判断与已访问的父结点构成环。时间复杂度$O(V+E)$，也可以输出环。

```cpp
vector<vector<int>> g(n);
vector<int> parent(n, -1);
vector<bool> vis(n, false);

bool bfs(int u) {
    queue<int> q; q.push(u);
    vis[u] = true;
    while (!q.empty()) {
        int cur = q.front(); q.pop();
        for (int nei : g[cur]) {
            if (!vis[nei]) {
                vis[nei] = true;
                q.push(nei);
                parent[nei] = cur;
            } else if (nei != parent[cur]) { // nei被访问过且不是cur的父结点, 有环
                return true;
            }
        }   
    }
    return false;
}

bool hasCycle() {
    for (int i = 0; i < n; ++i) {
        if (!vis[i]) {
            if (bfs(i)) {
                return true;
            }
        }
    }
    return false;
}
```

3. [并查集判环](https://eimadrigal.github.io/posts/union-find/)

### 有向图判环

1. BFS拓扑排序

2. DFS拓扑排序（vis + stack）

```cpp
vector<vector<int>> g(n);
vector<bool> vis(n, false);
vector<bool> onStack(n, false);

bool dfs(int s) {
    vis[s] = true;
    onStack[s] = true;  // 本次dfs访问的点
    for (int adj : g[s]) {
        if (!vis[adj]) {
            if (dfs(adj)) return true;
        } else if (onStack[adj]) {
            return true;
        }
    }
    onStack[s] = false;
    return false;
}

bool hasCycleDirected() {
    for (int i = 0; i < n; ++i) {
        if (!vis[i]) {
            if (dfs(i)) {
                return true;
            }
        }
    }
    return false;
}
```

如果要找具体的环，可以用`prev`数组保存每一步的走法。

3. DFS拓扑排序（三色vis法）

之所以要用三色法标记，是为了区分不同的dfs遍历，比如1->2, 1->3, 2->3这张图如果用无向图判环的方式就会误杀。

## 有向图强连通分量

在有向图G中，如果两个顶点互相有路径可达，称两个顶点是**强连通**的。如果G中任意两个顶点都是强连通的，则G是一个**强连通图**。G的极大强连通子图称为G的**强连通分量**（strongly connected components）。

寻找SCC有2种常见算法，一种是Kosaraju's algorithm，另一种就是Tarjan's SCC algorithm。

Tarjan算法中，每个结点u都有一个low-link value，表示DFS时从u出发能够到达的最小的id，包括u自己。

```cpp
vector<vector<int>> sccs;
int timestamp = 0;
void tarjan(int cur, vector<vector<int>>& g, vector<int>& dfn, vector<int>& low, stack<int>& stk, vector<bool>& inStk) {
    dfn[cur] = low[cur] = ++timestamp;
    stk.push(cur);
    inStk[cur] = true;
    for (int nei : g[cur]) {
        if (dfn[nei] == -1) {
            tarjan(nei, g, dfn, low, stk, inStk);
            low[cur] = min(low[cur], low[nei]);
        } else if (inStk[nei]) {
            low[cur] = min(low[cur], dfn[nei]);
        }
    }
    if (dfn[cur] == low[cur]) {
        vector<int> scc;
        int poped;
        do {
            poped = stk.top(); stk.pop();
            scc.push_back(poped);
            inStk[poped] = false;
        } while (poped != cur);
        sccs.push_back(scc);
    }
}

int main() {
    vector<vector<int>> g{{1,2}, {3}, {3,4}, {0,5}, {5}, {}};
    int n = g.size();
    vector<int> dfn(n, -1), low(n, -1);
    vector<bool> inStk(n, false);
    stack<int> stk;
    for (int i = 0; i < n; ++i) {
        if (dfn[i] == -1) {
            tarjan(i, g, dfn, low, stk, inStk);
        }
    }
    for (auto scc : sccs) {
        for (auto num : scc) {
            cout << num << " ";
        }
        cout << "\n";
    }
    return 0;
}
```

## Reference

[Detecting cycles in a graph using DFS](https://stackoverflow.com/questions/19113189/)  
[Tarjan’s Algorithm to find Strongly Connected Components](https://www.geeksforgeeks.org/tarjan-algorithm-find-strongly-connected-components/)
