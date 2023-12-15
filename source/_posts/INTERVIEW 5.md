---
title: INTERVIEW 5
url: interview-5
date: 2020-04-13 21:09:00
description: NetEase
categories: Computer Science
tags: [Interview]
---

## 笔试
150min，3题，每题100分，自己果然还是个蒟蒻呢~  
最近状态好差，虽然做了一些题，但还是考得稀烂，大概有几点需要加强：

 - **独立**做题，不要一边看板子一边写代码，更不要一开始就看题解；
 - 对之前研究过的一些**专项模板**，要非常熟练敲出来；
 - “题海”战术要继续，薄弱算法要**专项练习**：即使内推，也免不了笔试，保持手感很重要。

先补下题：

## No. 1
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200413151415498.png)
Pass 90%
开始觉得BFS可以做，但不知道怎么写，于是转去从`(x-l,y-l)`遍历到`(x+l,y+l)`，写了一下发现`l`会变，下一轮循环遍历范围可能增大，不知道怎么写下去，又转去DFS，因为DFS每一次递归都可以自动更改`l`的值，不知道为毛没有AC。
其实只要每一次都搜索整个图，去吃满足条件的补给品，直到**剑的长度不变**。  
我TM竟然没想到用长度作为终止条件，而且暴力时候太谨慎，不敢把整个图过一遍(只有500*500的数据范围啊，蠢哭了)，自己给自己增加难度~

```cpp
#include <iostream>
#include <vector>
#include <cmath>
using namespace std;

double dist(int x1, int y1, int x2, int y2) {
	return sqrt((x1 - x2) * (x1 - x2) + (y1 - y2) * (y1 - y2));
}

int main(void)
{
	int t;
	cin >> t;
	while (t--) {
		int m, l;
		cin >> m >> l;
		vector<vector<int>> g(m, vector<int>(m));
		for (int i = 0; i < m; ++i) {
			for (int j = 0; j < m; ++j) {
				cin >> g[i][j];
			}
		}

		int x, y;
		cin >> x >> y;
		int ans = -1;
		while (l != ans) {
			ans = l;
			for (int i = 0; i < m; ++i) {
				for (int j = 0; j < m; ++j) {
					if (g[i][j] > 0 && dist(x, y, i, j) <= l) {
						l += g[i][j];
						g[i][j] = -1;
					}
				}
			}
		}
		cout << ans << endl;
	}
	return 0;
}
```

## No. 2
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200413151429730.png)
Pass 10%  
并查集都能写挂，真无语了。。。唉，菜是原罪  
后来发现是自己模板有问题，结果一直T：**大多数情况下T的原因不在于输入输出，而在于算法**，血的教训。。。  
回头看以为是个裸题，但是涉及到并查集的删除操作，索性直接用`vector<int>`存每个元素到其集合的映射关系，这样看来更是简单(一看到题就陷入树结构的并查集。。。)：  
做题要看**数据范围**！！！$10^7$普通并查集$O(n)$可以过！！！而且题目很明显涉及到**删除**和**求集合中元素个数**的操作，在树结构的并查集中实现复杂！！！

```cpp
#include <iostream>
#include <vector>
using namespace std;

class UF {
public:
	UF(int n) {
		for (int i = 0; i <= n; ++i) {
			setNum.emplace_back(i);
		}
	}

	void unio(int x, int y) {
		if (setNum[x] != setNum[y]) {
			for (int i = 1; i < setNum.size(); ++i) {
				if (setNum[i] == setNum[y]) {
					setNum[i] = setNum[x];
				}
			}
		}
	}

	void getOut(int x) {
		if (size(x) != 1) {
			setNum[x] = -1;
		}
	}

	int size(int x) {
		int cnt = 0;
		for (int i = 1; i < setNum.size(); ++i) {
			if (setNum[x] == setNum[i]) {
				++cnt;
			}
		}
		return cnt;
	}

private:
	vector<int> setNum;
};

int main(void)
{
	int t;
	cin >> t;
	while (t--) {
		int n, m;
		cin >> n >> m;
		UF uf(n);
		for (int i = 0; i < m; ++i) {
			int op, x, y;
			cin >> op;
			if (op == 1) {
				cin >> x >> y;
				uf.unio(x, y);
			}
			else if (op == 2) {
				cin >> x;
				uf.getOut(x);
			}
			else {
				cin >> x;
				cout << uf.size(x) << endl;
			}
		}
	}
	return 0;
}
```

## No. 3
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020041315143877.png)
开始就知道暴力过不了，想着骗点分算了，枚举A的所有错排$\{B_1,B_2...\}$，计算A与B的最小距离即可，不知道为什么WA，结果爆零。。。  
贴下暴力代码：

```cpp
#include <iostream>
#include <algorithm>
#include <vector>
#include <unordered_map>
#include <limits>  // INT_MAX
using namespace std;

int main(void)
{
	int t;
	cin >> t;
	while (t--) {
		int n;
		cin >> n;
		vector<int> a(n);

		unordered_map<int, int> w(n);

		for (int i = 0; i < n; ++i) {
			cin >> a[i];
		}
		vector<int> b(a);
		for (int i = 0; i < n; ++i) {
			cin >> w[a[i]];
		}
		int ans = INT_MAX;

		sort(a.begin(), a.end());
		do {
			bool flag = true;
			for (int i = 0; i < n; ++i) {
				if (a[i] == b[i]) {
					flag = false;
					break;
				}
			}
			if (flag) {
				int cur = 0;
				for (int i = 0; i < n; ++i) {
					int tmp = find(a.begin(), a.end(), b[i]) - a.begin() - i;
					cur += w[b[i]] * abs(tmp);
				}
				ans = min(ans, cur);
			}
		} while (next_permutation(a.begin(), a.end()));

		cout << ans << endl;
	}

	return 0;
}
```
比较正派的做法我有想到一点，不过当时第2题没过，心情有点烦躁，净想着骗分去了~  
要使`dist`最小，就要求错排的每个位置移动尽可能少，使得`pos`之差尽可能小。  
如果n是偶数，相邻位置两两互换，`pos`之差为1；  
如果n是奇数，会多一个奇数位置的坑(有点贪心的意思)，这样必然要有一个奇数位置的数移动2次，当然选择权值最小的那个数：

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main(void)
{
	int t;
	cin >> t;
	while (t--) {
		int n;
		cin >> n;
		vector<int> a(n);
		vector<int> w(n);

		for (int i = 0; i < n; ++i) {
			cin >> a[i];
		}
		int sum = 0;
		for (int i = 0; i < n; ++i) {
			cin >> w[i];
			sum += w[i];
		}
		if (n & 1) {
			int minIdx = 0;
			for (int i = 0; i < n; i += 2) {
				if (w[i] < w[minIdx]) {
					minIdx = i;
				}
			}
			sum += w[minIdx];
		}

		cout << sum << endl;
	}

	return 0;
}
```

## 面试
收到面试通知很意外，唯一一个机会，还是面的完爆了呢。  
这几天一直在看面经，说实话不难，而且很多人都没手撕代码，就有了侥幸心理。。只看了面经的代码，easy的题目都没有做出来。  
第一次真正面试中写代码，紧张情绪下与平时状态完全不一样，第一次理解错题意，也没有确认就开始写，写完后才发现搞错了。。。  
不知道是面试官表达能力有问题，还是我理解能力有问题，从一开始的基础知识、到后来的智力题、再到编程题，五次三番误解他的意思，总之聊的很不愉快！！！面试有时候也看运气吧~  
简单做个总结吧：

 - 回答问题、手撕代码前一定要问清楚！！！！确认函数签名等细节，还可以先描述下自己的思路；
 - 只看面经不行，复习范围很局限，还是要系统学习、疯狂练习，平时有100%状态，面试才可能有70%状态；
 - 多参加面试，锻炼下高压下的思路和码力，任何时候都要冷静分析。

很难过了，后面应该还会再投一些公司吧。

[参考](https://www.zxpblog.cn/2020/04/11/%E7%BD%91%E6%98%93%E4%BA%92%E5%A8%B1%E7%AC%94%E8%AF%95-2020-4-11/)
