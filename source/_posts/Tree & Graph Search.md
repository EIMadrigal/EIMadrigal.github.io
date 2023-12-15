---
title: Tree & Graph Search
url: tree-and-graph-search
date: 2019-05-03 23:05:00
description: 暴搜
categories: Computer Science
tags: [Algorithm]
---

## Tree Search

### 结点定义

```cpp
// 二叉树
struct Node {
	int val;
	Node *left, *right;
	Node(): val(0), left(nullptr), right(nullptr) {}
	Node(int x): val(x), left(nullptr), right(nullptr) {}
	Node(int x, Node *left, Node *right): val(x), left(left), right(right) {}
};

// 多叉树
class Node {
public:
    int val;
    vector<Node*> children;

    Node() {}
    Node(int _val) {
        val = _val;
    }
    Node(int _val, vector<Node*> _children) {
        val = _val;
        children = _children;
    }
};
```

### 层序遍历

1. 二叉树的层序遍历

```cpp
// 迭代
vector<int> travel(Node *root) {
    if (!root) return {};
	vector<int> ans;
	queue<Node *> q;
	q.push(root);
	while (!q.empty()) {
		Node *cur = q.front(); q.pop();
		ans.push_back(cur->val);
		if (cur->left) q.push(cur->left);
		if (cur->right) q.push(cur->right);
	}
	return ans;
}
```

2. 多叉树的层序遍历

```cpp
vector<vector<int>> level_order(Node* root) {
    if (!root) return {};
    vector<vector<int>> ans;
    queue<Node*> q;
    q.push(root);
    int depth = 0;

    while (!q.empty()) {
        int size = q.size();
        ans.push_back({});
        while (size--) {
            Node* cur = q.front(); q.pop();
            ans[depth].emplace_back(cur->val);
            for (Node* child : cur->children) {
                q.push(child);
            }
        }
        ++depth;
    }
    return ans;
}
```

### 二叉树先序/中序/后序/Morris

1. 先序遍历
```cpp
// 递归
void travel(Node *root, vector<int>& vec) {
	if (!root) return;
	vec.push_back(root->val);
	travel(root->left, vec);
	travel(root->right, vec);
}

// 迭代
vector<int> preorder(Node *root) {
    if (!root) return {};
    vector<int> ans;
    stack<Node*> s;
    s.push(root);
    while (!s.empty()) {
        Node *cur = s.top(); s.pop();
        ans.emplace_back(cur->val);
        if (cur->right) s.push(cur->right);
        if (cur->left) s.push(cur->left);
    }
    return ans;
}
```

2. 中序遍历
```cpp
// 递归
void travel(Node *root, vector<int>& vec) {
	if (!root) return;
	travel(root->left, vec);
	vec.push_back(root->val);
	travel(root->right, vec);
}

// 迭代
vector<int> travel(Node *root) {
	vector<int> ans;
	stack<Node *> s;
	Node *cur = root;
	while (cur || !s.empty()) {
		while (cur) {
			s.push(cur);
			cur = cur->left;
		}
		cur = s.top(); s.pop();
		ans.push_back(cur->val);
		cur = cur->right;
	}
	return ans;
}
```

3. 后序遍历
```cpp
// 递归
void travel(Node *root, vector<int>& vec) {
	if (!root) return;
	travel(root->left, vec);
	travel(root->right, vec);
	vec.push_back(root->val);
}

// 迭代1
vector<int> travel(Node *root) {
	vector<int> ans;
	stack<Node *> s;
	Node *cur = root;
	Node *last_vis = nullptr;

	while (cur && last_vis != root) {
		while (cur && cur != last_vis) {
			s.push(cur);
			cur = cur->left;
		}
		cur = s.top(); s.pop();
		if (cur->right == nullptr || cur->right == last_vis) {
			ans.push_back(cur->val);
			last_vis = cur;
		} else {
			s.push(cur);
			cur = cur->right;
		}
	}
	return ans;
}

// 迭代2
vector<int> postorderTraversal(TreeNode* root) {
    if (!root) return {};
    stack<TreeNode*> s;
    deque<int> res;
    s.push(root);
    while (!s.empty()) {
        TreeNode *cur = s.top(); s.pop();
        res.push_front(cur->val);
        if (cur->left) s.push(cur->left);
        if (cur->right) s.push(cur->right);  
    }
    return vector<int>(res.begin(), res.end());
}
```

4. [Morris遍历0:39:00开始](https://www.bilibili.com/video/BV1NU4y1M7rF?p=14)

![在这里插入图片描述](https://img-blog.csdnimg.cn/8138f0b04af941bc91e80728cdf8ea59.png)
```cpp
void morris(TreeNode* root) {
    if (!root) return;
    TreeNode* cur = root, *mostRight = nullptr;
    while (cur) {
        mostRight = cur->left;
        if (mostRight) {
            while (mostRight->right && mostRight->right != cur) {
                mostRight = mostRight->right;
            }
            // mostRight是cur左子树最右结点
            if (mostRight->right) {  // 第一次来到cur
                mostRight->right = cur;
                cur = cur->left;
                continue;
            } else {  // mostRight->right == cur
                mostRight->right = nullptr;
            }
        }
        cur = cur->right;      
    }
}

void morrisPreorder(TreeNode* root) {
    if (!root) return;
    TreeNode* cur = root, *mostRight = nullptr;
    while (cur) {
        mostRight = cur->left;
        if (mostRight) {
            while (mostRight->right && mostRight->right != cur) {
                mostRight = mostRight->right;
            }
            // mostRight是cur左子树最右结点
            if (mostRight->right) {  // 第一次来到cur
                cout << cur->val << " ";
                mostRight->right = cur;
                cur = cur->left;
                continue;
            } else {  // mostRight->right == cur
                mostRight->right = nullptr;
            }
        } else {
            cout << cur->val << " ";
        }
        cur = cur->right;      
    }
}

void morrisInorder(TreeNode* root) {
    if (!root) return;
    TreeNode* cur = root, *mostRight = nullptr;
    while (cur) {
        mostRight = cur->left;
        if (mostRight) {
            while (mostRight->right && mostRight->right != cur) {
                mostRight = mostRight->right;
            }
            // mostRight是cur左子树最右结点
            if (mostRight->right) {  // 第一次来到cur
                mostRight->right = cur;
                cur = cur->left;
                continue;
            } else {  // mostRight->right == cur
                mostRight->right = nullptr;
            }
        }
        cout << cur->val << " ";
        cur = cur->right;      
    }
}

void morrisPostorder(TreeNode* root) {
    
}
```

### 多叉树先根/后根

1. 先根遍历

```cpp
// 递归
void preorder(Node* root, vector<int>& ans) {
    if (!root) return;
    ans.emplace_back(root->val);
    for (Node* child : root->children) {
        preorder(child, ans);
    }
}

// 迭代
vector<int> preorder(Node* root) {
    if (!root) return {};

    vector<int> ans;
    stack<Node*> s;
    s.push(root);

    while (!s.empty()) {
        Node* cur = s.top();
        s.pop();
        ans.emplace_back(cur->val);
        for (auto it = cur->children.rbegin(); it != cur->children.rend(); ++it) {
            s.push(*it);
        }
    }
    return ans;
}
```

2. 后根遍历

```cpp
// 递归
void postorder(Node *root, vector<int>& ans) {
    if (!root) return;
    for (Node *child : root->children) {
        postorder(child, ans);
    }
    ans.emplace_back(root->val);
}

// 迭代
vector<int> postorder(Node *root) {
    if (!root) return {};
    vector<int> ans;
    stack<Node*> s;
    s.push(root);

    while (!s.empty()) {
        Node *cur = s.top();
        s.pop();
        ans.emplace_back(cur->val);
        for (auto it = cur->children.begin(); it != cur->children.end(); ++it) {
            s.push(*it);
        }
    }
    reverse(ans.begin(), ans.end());
    return ans;
}
```

## Graph Search
笔试一般化为平时擅长的图表示再去做算法。

### 图的表示及转换
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200218153739820.png)

 - 邻接矩阵

|i/j|0|1|2|3|4|
|--|--|--|--|--|--|
|0|0|-1|4|$\infty$|$\infty$|
|1|$\infty$|0|3|2|2|
|2|$\infty$|$\infty$|0|$\infty$|$\infty$|
|3|$\infty$|1|5|0|$\infty$|
|4|$\infty$|$\infty$|$\infty$|-3|0|

用`vector<vector<int>> g(n, vector<int>(n, INF))`表示，`g[i][j]`表示从顶点$i$到顶点$j$的权重，空间复杂度$O(|V|^2)$，适用于稠密图，用的不多；

 - 邻接表：链表比较少用，基本都用动态数组。

|0|(1,-1)|(2,4)|-|-|-|
|--|--|--|--|--|--|
|1|(2,3)|(3,2)|(4,2)|-|-|
|2|-|-|-|-|-|
|3|(1,1)|(2,5)|-|-|-|
|4|(3,-3)|-|-|-|-|

用`vector<vector<pair<int, int>>> g`表示，`g[i][j].first`表示从顶点$i$出发到达的顶点$k$，`g[i][j].second`表示从顶点$i$到顶点$k$的权值，空间复杂度$O(|V|+|E|)$。
```cpp
// 邻接矩阵转邻接表
vector<vector<pair<int, int>>> to_adj_list(vector<vector<int>>& adj_matrix) {
    int n = adj_matrix.size();
    vector<vector<pair<int, int>>> ans(n);
    for (int i = 0; i < n; ++i) {
        vector<pair<int, int>> tmp;
        for (int j = 0; j < n; ++j) {
            if (adj_matrix[i][j] != INT_MAX)
                tmp.emplace_back(make_pair(j, adj_matrix[i][j]));
        }
        ans[i] = tmp;
    }
    return ans;
}

// 邻接表转邻接矩阵
vector<vector<int>> to_adj_matrix(vector<vector<pair<int, int>>>& adj_list) {
    int n = adj_list.size();
    vector<vector<int>> ans(n, vector<int>(n, INT_MAX));
    for (int i = 0; i < n; ++i) {
        for (pair<int, int>& p : adj_list[i]) {
            ans[i][p.first] = p.second;
        }
    }
    return ans;
}
```

 - 边表  
每个三元组表示一条边，上图的所有边表示为：$(0,1,-1),(0,2,4),(1,2,3),(1,3,2),(1,4,2),(3,1,1),(3,2,5),(4,3,-3)$
用`vector<vector<int>> e`表示，`e[i][0]`表示顶点$u$，`e[i][1]`表示顶点$v$，`e[i][2]`表示$u$到$v$的权值，空间复杂度$O(|E|)$。

 - 链式前向星  
空间复杂度$O(n)$，结合了邻接表和边表，包括边表数组`edge`和头结点数组`head`，
![img](https://img-blog.csdnimg.cn/20200725135220669.png)
从结点2出发的边有4条，第一条边`head[2]=8`意味着该边存在`edge[8]`，下一条边存在`edge[6]`，`next==-1`表示结束。
```cpp
struct edge {
	int to, next, w;  // 边终点to 下一条边next 权值w
} edge[1000000];

int head[1000000];  // head[i]表示指向i的第一条边的存储位置
int cnt;  // 记录edge的末尾位置

void init() {
	for (int i = 0; i < 1000000; ++i) {
		edge[i].next = -1;
		head[i] = -1;
	}
	cnt = 0;
}

void addEdge(int u, int v, int w) {
	edge[cnt].to = v;
	edge[cnt].w = w;
	edge[cnt].next = head[u];
	head[u] = cnt++;
}

// 遍历结点i的所有邻接点
for (int i = head[u]; i != -1; i = edge[i].next) {

}
```

### BFS
```python
def bfs(g, s):
    queue = []
    marked = set()  # 避免环造成死循环
    parent = {s : None}  # for shortest path
    queue.append(s)
    marked.add(s)
    while (len(queue) > 0):
        cur = queue.pop(0)
		print(cur)
        for adj in g[cur]:
            if adj not in marked:
                marked.add(adj)
                queue.append(adj)
                parent[adj] = cur
    return parent
```

双向BFS
```cpp
int dbfs(int s, int e) {
    if (s == e) {
        return 0;
    }
    queue<int> q1, q2;
    q1.push(s), q2.push(e);
    unordered_set<int> vis1{s}, vis2{e};
    int res = 0;
    while (!q1.empty() && !q2.empty()) {
        if (q1.size() < q2.size()) {
            int size = q1.size();
            while (size--) {
                int cur = q1.front(); q1.pop();
                for (int nei : cur.neighbors) {
                    if (vis1.count(nei)) continue;
                    if (vis2.count(nei)) return res;
                    vis1.insert(nei);
                    q1.push(nei);
                }
            }
        } else {
            int size = q2.size();
            while (size--) {
                int cur = q2.front(); q2.pop();
                for (int nei : cur.neighbors) {
                    if (vis2.count(nei)) continue;
                    if (vis1.count(nei)) return res;
                    vis2.insert(nei);
                    q2.push(nei);
                }
            }
        }
        ++res;
    }
    return -1;
}
```

### DFS

从起点出发，标记走过的点，如果发现没有走过的点，随便选一个向前走，无路可走就回退。

```python
def dfs(g, s):
    stack = []
    marked = set()
    stack.append(s)
    marked.add(s)
    while (len(stack) > 0):
        cur = stack.pop()
		print(cur)
        for adj in g[cur]:
            if adj not in marked:
                marked.add(adj)
                stack.append(adj)
```
很不幸的是：上面的代码是错的。举个例子：g有ABCDEF6个结点，边为AB AC BC BD CD CE DE DF，如果走ABDE的话，最终答案应该是ABDECF，但是上述代码的结果是ABDEFC，显然不是合法的DFS结果。

问题在于标记结点是否访问的时机不对，在D弹出后，直接把EF入栈并标记为已访问，下次到E时发现C已被标记，但此时C很明显并未访问。  
不应在入栈时标记，而应该在弹出时标记。因为入栈时并没有真正地访问该节点，出栈时才真正访问。  
可以参考[CS61B](https://github.com/joepachou/NoteBook/issues/116)，正确的代码如下，可能会导致重复入栈（有方法避免）：
```python
def dfs(g, s):
    stack = []
    marked = set()
    stack.append(s)

    while (len(stack) > 0):
        cur = stack.pop()
		if cur not in marked:
            marked.add(cur)
		    print(cur)
            for adj in g[cur]:
                if adj not in marked:
                    stack.append(adj)

def dfs(g, s):
    global marked
    marked = set()
    marked.add(s)
    print(s)
    for adj in g[s]:
        if adj not in marked:
            dfs(g, adj)
```

 - 判断从V出发能否走到终点
```python
bool dfs(v) {
	if (v is terminal) return true;
	if (vis[v]) return false;
	vis[v] = true;
	for (u in adj(v)) {
		if (dfs(u)) return true;
	}
	return false;
}
```

 - 判断从V出发能否走到终点，若能，记录路径

栈的作用就是在走投无路之时留给你的退路。

```cpp
Node path[MAX_LEN];  // MAX_LEN取节点总数即可
int depth;  // 当前点的深度

bool dfs(V) {
	if (V为终点) {
		path[depth] = V;
		return true;
	}
	if (V为旧点) {
		return false;
	}

	将V标记为旧点;
	path[depth++] = V;

	对和V相邻的每个节点U {
		if (Dfs(U))
			return true;
	}

	--depth;   //从V走不到终点，把V排除出数组,回退到V的父节点
	return false;
}

int main() {
	所有点标记为新点;
	depth = 0;
	if (Dfs(起点)) {
		for (int i = 0; i <= depth; i++) {
			cout << path[i] << endl;
		}
	}
	return 0;
}
```

 - 遍历图上所有节点
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190409225110387.png)

邻接矩阵存储遍历复杂度$O(n^2)$，因为对每个节点，都要判断其它所有节点是否相邻。
邻接表遍历复杂度$O(n+e)$。

1、[城堡问题](http://poj.org/problem?id=1164)
给一个地图以及每个格子周围的墙所代表数字之和，求该地图有多少房间，最大房间的面积。

分析：
要先判断每个格子周围有什么墙，注意到1，2，4，8的二进制形式`0001`、`0010`、`0100`、`1000`，所以只要将输入数字与1，2，4，8相与，就能知道该方块周围有什么墙。
把方块看作节点，相邻两个方块如果没有墙，就在这两节点之间连一条边，转换为图。
房间个数：图中的极大连通子图个数
**极大连通子图：一个连通子图，加任意一个图中的其他点就不连通，这个子图就是极大连通子图。**

具体：
对每个房间进行DFS，得到该房间所在的极大连通子图，染色所有能够到达的房间，最后统计共用了几种颜色以及每种颜色的数量。

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <cstdio>
#include <iostream>
#include <algorithm>

using namespace std;

int room[50][50];
int color[50][50] = { 0 };   //标记方块是否染色,初始都未被访问
int maxRoomArea = 0, roomNum = 0, curRoomArea = 0;

void Dfs(int i,int j)   //从i,j出发遍历极大连通子图
{
	if (color[i][j])
		return;

	color[i][j] = roomNum;   //该方块染色
	curRoomArea++;
	if ((room[i][j] & 1) == 0) Dfs(i, j - 1);  //没有西墙，向西走
	if ((room[i][j] & 2) == 0) Dfs(i - 1, j);
	if ((room[i][j] & 4) == 0) Dfs(i, j + 1);
	if ((room[i][j] & 8) == 0) Dfs(i + 1, j);
}

int main()
{
	int row, column;
	scanf("%d%d", &row, &column);
	for (int i = 0; i < row; i++)
		for (int j = 0; j < column; j++)
		{
			scanf("%d", &room[i][j]);
		}

	for (int i = 0; i < row; i++)
		for (int j = 0; j < column; j++)
		{
			if (!color[i][j])   //找到一个新的房间
			{
				roomNum++;
				curRoomArea = 0;
				Dfs(i, j);          //探索该房间（极大连通子图）
			}
			maxRoomArea = max(curRoomArea, maxRoomArea);
		}

	printf("%d\n%d", roomNum, maxRoomArea);

	return 0;
}
```

2、[踩方格](http://bailian.openjudge.cn/practice/4103)
递归，从$(i,j)$出发走n步的方案数就等于先走一步，从其它三个格子走n-1步的方案数之和。
前提就是该方块没走过。

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <cstdio>
#include <iostream>
#include <algorithm>

using namespace std;

bool isVisited[20][20] = { 0 };

int Dfs(int i, int j, int n)
{
	int ans = 0;
	//访问过直接返回
	if (isVisited[i][j])
		return 0;
	//递归边界
	if (0 == n)
		return 1;

	isVisited[i][j] = true;

	//可以走三个方向
	ans += Dfs(i - 1, j, n - 1);
	ans += Dfs(i, j - 1, n - 1);
	ans += Dfs(i, j + 1, n - 1);

	//返回前表示当前格子可以重新被访问，以后的走法可能会访问到
	isVisited[i][j] = false;

	return ans;
}

int main()
{
	int n;
	scanf("%d", &n);

	printf("%d\n", Dfs(20, 20, n));

	return 0;
}
```
3、[ROADS](http://poj.org/problem?id=1724)
很多时候，并不需要一条路走到黑，这就是深搜中的**剪枝**。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190414173437346.png)

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <cstdio>
#include <algorithm>
#include <iostream>
#include <vector>
#include <sstream>
#include <string>

using namespace std;

/*存储边,不需要起点,G(i)表示从i出发*/
struct Road {
	int destination, len, toll;
};

/*邻接表存储图*/
vector<vector<Road>> G(110);

int k, n, r;
int minLen;   //探索过的最短的路径
int totalLen;   //正在探索的最短路径
int totalCost;   //正在探索的花费
int visited[110];
int minL[110][10010]; //minL[i][j]:从1走到城市i，且花了j块钱的最优路径长度

void dfs(int s)
{
	if (s == n)   //找到了路径
	{
		minLen = min(minLen, totalLen);
		return;   //强制结束函数
	}

	int len = G[s].size();
	for (int i = 0; i < len; i++)
	{
		Road r = G[s][i];

		/*判断有没有足够的钱走到r.destination*/
		if (totalCost + r.toll > k) //钱不够，试下一条边
			continue;     //可行性剪枝

		if (!visited[r.destination])
		{
			/*最优性剪枝*/
			//当前走过的路长度已经大于之前的minLen，就没必要走下去
			if (totalLen + r.len >= minLen)
				continue;

			//走到r.d时花费同样的钱走过的路长度大于之前相同花费的路长度
			if (totalLen + r.len >= minL[r.destination][totalCost + r.toll])
				continue;

			minL[r.destination][totalCost + r.toll] = totalLen + r.len;

			totalLen += r.len;
			totalCost += r.toll;
			visited[r.destination] = 1;
			dfs(r.destination);

			/*不走r.destination*/
			visited[r.destination] = 0; //换下条边之前将访问标志清0
			totalLen -= r.len;
			totalCost -= r.toll;
		}
	}
}

/*从城市1开始深搜整个图，找到所有能到达n的，选最优的*/
int main()
{
	scanf("%d%d%d", &k, &n, &r);

	for (int i = 0; i < r; i++)
	{
		int source;
		Road r;

		scanf("%d%d%d%d", &source, &r.destination, &r.len, &r.toll);

		if (source != r.destination)
		{
			G[source].push_back(r);
		}
	}

	memset(visited, 0, sizeof(visited));
	totalLen = 0, totalLen = 0;
	minLen = 1 << 30;   //置为无穷大
	for (int i = 0; i < 110; i++)
		for (int j = 0; j < 10010; j++)
			minL[i][j] = 1 << 30;


	visited[1] = 1;
	dfs(1);  //走完了所有路

	if (minLen < (1 << 30))
	{
		printf("%d\n", minLen);
	}
	else
		printf("-1\n");

	return 0;
}
```

4、[生日蛋糕](http://poj.org/problem?id=1190)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190414175732766.png)![在这里插入图片描述](https://img-blog.csdnimg.cn/20190414182524737.png)
[练习1](http://bailian.openjudge.cn/practice/2816/)  
[练习2](http://bailian.openjudge.cn/practice/2488/)  
[练习3](http://bailian.openjudge.cn/practice/1321/)

## Refs
[郭炜老师MOOC](https://www.icourse163.org/learn/PKU-1001894005?tid=1205957211#/learn/content?type=detail&id=1210422520)  
[Binary Tree Traversals](http://faculty.cs.niu.edu/~mcmahon/CS241/Notes/Data_Structures/binary_tree_traversals.html)
