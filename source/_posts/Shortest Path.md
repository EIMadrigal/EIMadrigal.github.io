---
title: Shortest Path
url: shortest-path
date: 2020-02-18 21:46:00
description: 最短路径问题
categories: Computer Science
tags: [Algorithm]
---

## Dijkstra
BFS可以用来求无权图的最短路径，Dijkstra是**单源最短路径**算法，[一般的DIJ算法不能用于含负权边的图](https://www.zhihu.com/question/21620069)，本质上还是贪心思想：

 1. 将所有结点分为两类：已经确定最短路径的点集$u$、未确定最短路径的点$v$；
 2. 初始化$dis[start]=0$，其余结点$dis$设为无穷大；
 3. 找出一个$dis$最小的$v$中的点$x$，将其标记为$u$，遍历$x$的所有**邻接点**$y$，若$dis[y]>dis[x]+w[x][y]$，则更新$y$点到源点的最短路径长度$dis[y]=dis[x]+w[x][y]$；
 4. 重复，直到所有点都在$u$中。

邻接矩阵版，适合顶点比较少的图，复杂度$O(2V^2)$  
邻接表版，复杂度$O(V^2+E)$

寻找点$x$的循环可以用堆来优化，使得复杂度降为$O(VlogV+E)$。

堆的优化要注意2点:

 - 小根堆要按照$dis$组织，但是在调整时要保证$dis$到顶点索引的映射关系，所以堆存储$(index,dis)$
 - 松弛时堆里已经有某些顶点，这些顶点更新后的新值也要压入堆，原先堆里的顶点既无法更新，也无法删除。所以我们允许堆里出现相同的顶点，但是对于`marked`标记过的点直接`pop+continue`即可。

C++实现在这里

```cpp
// 邻接矩阵
vector<int> dijkstra(vector<vector<int>>& g, int s) {
    int n = g.size();
    vector<int> dis(n, INT_MAX);
    vector<bool> vis(n, false);
    dis[s] = 0;
    
    for (int i = 0; i < n; ++i) {
        // 寻找未访问点中dis最小的
        int minIdx = -1, minDis = INT_MAX;
        for (int j = 0; j < n; ++j) {
            if (!vis[j] && dis[j] < minDis) {
                minDis = dis[j], minIdx = j;
            }
        }
        // 剩余的点与起点不连通
        if (minIdx == -1) return {};
        vis[minIdx] = true;  // 锁死当前的最小点
        // 以minIdx为中介试图优化dis[j]
        for (int j = 0; j < n; ++j) {
            // 未锁死、j可达、路过minIdx可以使dis[j]更优
            if (!vis[j] && g[minIdx][j] != INT_MAX && dis[minIdx] + g[minIdx][j] < dis[j])
                dis[j] = dis[minIdx] + g[minIdx][j];
        }
    }
    return dis;
}

// 邻接表
vector<int> dijkstra(vector<vector<pair<int, int>>>& g, int s) {
    int n = g.size();
    vector<int> dis(n, INT_MAX);
    vector<bool> vis(n, false);
    dis[s] = 0;
    
    for (int i = 0; i < n; ++i) {
        // 寻找未访问点中dis最小的
        int minIdx = -1, minDis = INT_MAX;
        for (int j = 0; j < n; ++j) {
            if (!vis[j] && dis[j] < minDis) {
                minDis = dis[j], minIdx = j;
            }
        }
        
        if (minIdx == -1) return {};
        vis[minIdx] = true;
        // 直接获得minIdx能到达的点
        for (int j = 0; j < g[minIdx].size(); ++j) {
            int w = g[minIdx][j].first, id = g[minIdx][j].second;
            if (!vis[id] && dis[minIdx] + w < dis[id])
                dis[id] = dis[minIdx] + w;
        }
    }
    return dis;
}

// 邻接表 + 堆优化
typedef pair<int, int> p;
vector<int> dijkstra(vector<vector<p>>& g, int s) {
    int n = g.size();
    vector<int> dis(n, INT_MAX), parent(n);
    vector<bool> vis(n, false);
    dis[s] = 0;

    priority_queue<p, vector<p>, greater<p>> pq;
    pq.push({0, s});
    while (!pq.empty()) {
        int minIdx = pq.top().second, minDis = pq.top().first;
        pq.pop();
        if (vis[minIdx]) continue;
        vis[minIdx] = true;
        for (auto adj : g[minIdx]) {
            if (!vis[adj.second] && dis[minIdx] + adj.first < dis[adj.first]) {
                dis[adj.first] = dis[minIdx] + adj.first;
                pq.push({dis[adj.first], adj.second});
                parent[adj.second] = minIdx;
            }
        }
    }
    return dis;
}
```


```python
def dijkstra(g, s):
    pq = []
    heapq.heappush(pq, (0, s))

    marked = set()
    parent = {s : None}
    dis = {s : 0}
    for v in g:
        if v != s:
            dis[v] = float('inf')

    while (len(pq) > 0):
        pair = heapq.heappop(pq)
        cur_dis = pair[0]
        cur_vertex = pair[1]

		if cur_vertex in marked:
			continue

        marked.add(cur_vertex)

        for adj in g[cur_vertex].keys():
            if adj not in marked and cur_dis + g[cur_vertex][adj] < dis[adj]:
                heapq.heappush(pq, (cur_dis + g[cur_vertex][adj], adj))
                parent[adj] = cur_vertex
                dis[adj] = cur_dis + g[cur_vertex][adj]
    return parent, dis
```

## Bellman-Ford
可以计算含有负权的边，检测是否存在一个从源结点可以到达的权重为负值的环路（负权环）：

```cpp
bool bellmanFord(vector<vector<int>>& edge, vector<int>& dis, int start) {
	for (int i = 0; i < dis.size(); ++i) {
		if (i == start)
			dis[i] = 0;
		else
			dis[i] = 0x3f3f3f3f;
	}

	for (int i = 0; i < dis.size(); ++i)
		for (int j = 0; j < edge.size(); ++j)
			dis[edge[j][1]] = min(dis[edge[j][1]], dis[edge[j][0]] + edge[j][2]);

	bool negCycle = false;
	for (int i = 0; i < edge.size(); ++i)
		if (dis[edge[i][1]] > dis[edge[i][0]] + edge[i][2]) {
			negCycle = true;
			break;
		}

	return negCycle;
}
```
复杂度$O(VE)$。

## Floyd-Warshall
多源最短路径算法。
```cpp
void floyd(vector<vector<int>>& m) {
	for(size_t k = 0;k < m.size();++k)
		for(size_t i = 0;i < m.size();++i)
			for (size_t j = 0; j < m.size(); ++j) {
				// m不能用INT_MAX初始化，会溢出，用0x3f3f3f3f
				if (m[i][k] + m[k][j] < m[i][j])
					m[i][j] = m[i][k] + m[k][j];
			}
}
```
**不能用于有累加和为负的环的图**，这种图不存在最短路径。
