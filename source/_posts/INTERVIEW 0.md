---
title: INTERVIEW 0
url: interview-0
date: 2019-02-28 21:00:00
description: TP-Link
categories: Computer Science
tags: Interview
---

## 造成网络延迟的可能原因
1. WiFi所有用户上下行流量共用一个信道，当用户太多或者有人在下载大的资源时带宽不够，丢包；
2. 线路质量不佳导致信噪比太低，比如光纤损耗太大等。

## IPv6优势
1. IPv4地址不够用，IPv6有$2^{128}$个地址；
2. 使用更小的路由表，转发速度更快；
3. 扩充了DHCP协议，支持自动配置；安全性更高，有更好的头部格式，允许扩容......

## 找到单向无环链表的中间元素，若结点总数为偶数，返回第二个元素
[leetcode类似题目](https://leetcode.com/problems/middle-of-the-linked-list/)

只扫描一遍的做法：设两个指针，初始指向头结点，p1每次走两步，p2每次走一步，p1到达链尾，p2到达中间。假设链表带有头结点。
```cpp
/*单链表定义*/
struct ListNode{
	int val;
	ListNode* next;
	ListNode(int x) :val(x), next(NULL) {};
};

class Solution {
public:
	ListNode * middle(ListNode* head)
	{
		if (head == NULL)
			return NULL;
		ListNode* fast = head;
		ListNode* slow = head;
		while (fast &amp;&amp; fast-&gt;next)
		{
			fast = fast-&gt;next-&gt;next;
			slow = slow-&gt;next;
		}
		return slow;
	}
};
```

## 给出四个点坐标，判断是否是凸四边形
不妨扩展下该问题，给出任意n个点，判断[是否凸多边形](http://acm.hdu.edu.cn/showproblem.php?pid=2108)。

凸多边形就是所有内角均小于180&deg;，方法有好几种，这里利用定点凹凸性判断：
设当前三个连续的顶点$P_0, P_1, P_2$，计算向量$P_0P_1$, $P_1P_2$的叉积，若结果为正，表示多边形顶点逆时针转；若结果为负，两向量夹角大于180&deg;，则为凹多边形。
```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <cstdio>
#include <vector>

using namespace std;

struct point {
    int x, y;
}p[600000];

int cross_p(point a, point b,point c)
{
    return (b.x - a.x) * (c.y - b.y) - (c.x - b.x) * (b.y - a.y);
}

bool isConvex(int n)
{
    for (int i = 0; i < n; i++)
    {
        //叉积量值
        if (cross_p(p[i], p[(i + 1) % n], p[(i + 2) % n]) < 0)
            return false;
    }
    return true;
}

int main()
{
    int n;

    while (scanf("%d", &n) && n)
    {
        for (int i = 0; i < n; i++)
        {
            scanf("%d%d", &p[i].x, &p[i].y);
        }
        if (isConvex(n))
            printf("convex\n");
        else
            printf("concave\n");
    }

    return 0;
}
```

## 两个位数在10万位以内的数乘法
[高精度](https://leetcode.com/problems/multiply-strings/)

高精度乘法，模仿我们笔算的过程。每一位$res[i + j]$的构成：$res[i + j] + carry + a[i] * b[j]$，注意去掉结果的前导0。
```cpp
class Solution {
public:
    string multiply(string num1, string num2) {
        int a[120], b[120], res[250];
        memset(a, 0, sizeof(a));
        memset(b, 0, sizeof(b));
        memset(res, 0, sizeof(res));

        int lena = num1.size(), lenb = num2.size();
        for (int i = 0; i < lena; i++)
            a[i] = num1[lena - i - 1] - '0';
        for (int i = 0; i < lenb; i++)
            b[i] = num2[lenb - i - 1] - '0';

        for (int i = 0; i < lenb; i++)
        {
            int carry = 0;
            for (int j = 0; j < lena; j++)
            {
                res[i + j] = res[i + j] + a[j] * b[i] + carry;
                carry = res[i + j] / 10;
                res[i + j] %= 10;
            }
            res[i + lena] = carry;
        }

        int len_res = lena + lenb;
        //去掉结果的前导0,若结果为0，保留一个0
        while (res[len_res - 1] == 0 && len_res > 1)
        {
            len_res--;
        }
        　　　　　　　//使用字符串流将整数转为字符串
        stringstream ans;
        for (int i = len_res - 1; i >= 0; i--)
        {
            ans << res[i];
        }
        return ans.str();
    }
};
```

## 其它
1. 操作系统：CPU调度，用户态&内核态，IPC，各种锁，实时系统；
2. 数据结构：判断有向图是否存在回路（拓扑排序、求最短路、关键路径、BFS），排序（快排、冒泡、选择、插入），链表是否有环；
3. 计网：ARP、TCP/UDP、NAT、802.11ac协议，ping过程；
4. C++多态。
