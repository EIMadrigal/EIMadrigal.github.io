---
title: Codeforces Round 590
url: cf-round-590
date: 2019-10-02 13:09:00
description: CF
categories: Computer Science
tags: [Algorithm,Online Judge]
---

题目链接：[Round #590](https://codeforces.com/contest/1234)  
题目答案：[官方Editorial](https://codeforces.com/blog/entry/70233)、[My Solution](https://github.com/EIMadrigal/CF)

## A. Equalize Prices Again
签到题还WA了一发，向上取整有点问题：

```cpp
//my wrong code, 1.0 * sum返回double
ceil(1.0 * sum / n);

//right code
(int)ceil(1.0 * sum / n);

//ceil()原型
double ceil(double x);
```
`float`能保证6位精度（有效数字），`double`能保证15位精度。但是`float`和`double`**默认都只显示6位有效数字**，所以一旦`1.0 * sum / n`大于6位，函数返回的`double`就显示不全，造成精度损失（比如结果应该是`5336844`，但返回`5.33684e+006`），故进行强制类型转换。

## B1. Social Network (easy version)
题意：屏幕可以容纳$k$条短信，有若干朋友发来$n$条短信。如果某个朋友已经在屏幕上，不做改变；否则将其他朋友下移，新收到的朋友置顶。求最终自顶向下显示在屏幕上的朋友。  
思路：按照题意模拟，我搞得有些繁琐（用`queue + set`来考虑是否将新来的短信放入屏幕，再用`queue.size()`和$k$判断是否需要将旧短信`pop()`，最后将队列中的元素逆序输出）。

## B2. Social Network (hard version)
数据量变到了$10^5$级别，官方题解和我在B1中的思路一致。不过最后输出我是先压栈，题解是先存入`vector`，再用`reverse()`函数逆序，复杂度$O(nlogk)$。

## C. Pipes
模拟题：比赛时候发现了既然可以无限旋转，那么管道一共有2类：$12$一样，$3456$一样。  
水流到$12$这类，只能水平向右流；  
流到$3456$这类，那么另一行对应的位置也必须是$3456$类，水就会换一行流动（异或即可换行），否则水没法继续流动。  
最后判断水能否流到第二行第$n$列。

## D. Distinct Characters Queries
题意：给一字符串和$q$次查询，查询分为两类：一类是替换字符串中某个字母，另一类是求子串中非重复字符的个数。  
比赛时的思路是：遇到第一类查询就按规则替换，第二类先拿到子串，依次把子串的每个字符放入`set`中，最后返回`set.size()`即是非重复字符个数。  
此题的教训就是要学会根据数据量级猜算法：1s/2s时限，C++运算次数大约在$10^7$，本题的数据范围$10^5$，我傻傻地把$O(n^2)$的算法交了三次，TLE了三次，我还以为是`cin`的问题。。。  
参考下大佬经验：[由数据范围反推算法复杂度](https://www.acwing.com/blog/content/32/)。