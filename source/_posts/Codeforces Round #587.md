---
title: Codeforces Round 587
url: cf-round-587
date: 2019-09-23 22:13:00
description: CF
categories: Computer Science
tags: [Algorithm,Online Judge]
---

题目链接：[Round #587](https://codeforces.com/contest/1216)  
题目答案：[官方Editorial](https://codeforces.com/blog/entry/69954)、[My Solution](https://github.com/EIMadrigal/CF)

## A. Prefixes
题意：给一字符串，只含有`'a'`或`'b'`，需要改变某些位置（`'a'`变`'b'`或`'b'`变`'a'`），使得该字符串任意偶数长度前缀中`'a'`和`'b'`个数相等，求改变的最少次数以及更改后的字符串。

题解：遍历，判断`s[2i]`和`s[2i+1]`是否相等。如果相等，需要一次更改，并将其中一个改为不同字母。

## B. Shooting
题意：给$n$个编号$1$~$n$的射击目标，每个目标有不同的耐久度，假设已经击倒了$x$个目标，将要射击第$i$个目标，那么需要$a_i*x+1$次才可以击倒该目标，$a_i$为第$i$个目标的耐久度。求击倒所有目标的最少射击次数以及射击次序。

题解：贪心。想要射击次数最少，就要先射击耐久度大的目标，所以按照耐久度由大到小排序，同时记录对应的目标序号。

## C. White Sheet
题意：给定三个矩形，两黑一白，判断两个黑色矩形是否可以完全覆盖白色矩形，不能完全覆盖输出`YES`。

题解：分两种情况，记白色矩形面积为$S_w$，一个黑色矩形与白色矩形交叉面积为$S_{wb1}$，另一个黑色矩形与白色矩形交叉面积为$S_{wb2}$，两黑色矩形交叉面积为$S_{bb}$：  
1、两黑色矩形无交叉：只要$S_{wb1}+S_{wb2}<S_w$，就可以看到白色矩形；  
2、两黑色矩形有交叉：如果$S_{wb1}+S_{wb2}-S_{bb}<S_w$，可以看到白色矩形。