---
title: Pattern Matching
url: pattern-matching
date: 2018-04-02 21:03:32
description: 字符串模式匹配
categories: Computer Science
tags: [Algorithm]
---

字符串模式匹配，即子串的定位操作。就是判断主串S中是否存在给定的子串，如果存在，那么返回子串在S中的位置，否则返回-1。  
实现这种操作有两种算法：

## 朴素的模式匹配算法
设主串S长度为n，子串T长度为m。  
对于主串的每个字符，做长度为m的循环，判断是否与子串匹配。  
最好的情况就是一开始就匹配成功，时间复杂度$O(1)$；最坏的情况就是每次匹配失败都是在T的最后一个元素，复杂度$O(nm)$;平均情况复杂度$O(n+m)$。
```cpp
int match(string s, string t) {
    if (t.size() > s.size())
        return -1;

    int i = 0, j = 0;
    while (i < s.size() && j < t.size()) {
        if (t[j] == s[i]) {
            ++i;
            ++j;
        }
        else {
            i = i - j + 1;
            j = 0;
        }
    }

    if (j == t.size())
        return i - j;
    else
        return -1;
}
```

## KMP算法
KMP主要分两步：

1. 进行t的自匹配，这一步关键在于得到next数组。
next中的值是字符串的前缀集合与后缀集合的交集中最长元素的长度，将Next[0] = -1。  
举例来说：t=ababaca，前缀为pre，后缀为post。  
i = 1: 要处理"a", pre = {""}, post = {""}, next[1] = 0;  
i = 2: 要处理"ab", pre = {a}, post = {b}, Next[2] = 0;  
i = 3: 要处理"aba", pre = {a, ab}, post = {a, ba}, Next[3] = 1;  
i = 4: 要处理"abab", pre = {a, ab, aba}, post = {b, ab, bab}, Next[4] = 2;  
i = 5: 要处理"ababa", pre = {a, ab, aba, abab}, post = {a, ba, aba, baba}, Next[5] = 3;  
i = 6: 要处理"ababac", pre = {a, ab, aba, abab, ababa}, post = {c, ac, bac, abac, babac}, Next[6] = 0;  
i = 7: 要处理"ababaca", pre = {a, ab, aba, abab, ababa, ababac}, post = {a, ca, aca, baca, abaca, babaca}, Next[7] = 1;  
Next数组$[-1,0,0,1,2,3,0,1]$
2. S与T的匹配，这步的匹配和朴素匹配没有太大差异，只是主串S的指针不用回溯，而将子串的指针j回溯到Next[j]位置。

从T的第一位开始对自身匹配，在某一位置能匹配的最长长度即是当前位置Next值。

```cpp
vector<int> getNext(const string& t) {
    vector<int> next(t.size());
    next[0] = -1, next[1] = 0;

    int i = 2, j = 0;
    while (i < t.size()) {
        if (t[i - 1] == t[j]) {
            next[i++] = ++j;
        } else if (j > 0) {
            j = next[j];
        } else {
            next[i++] = 0;
        }
    }
    return next;
}

int kmp(string s, string t) {
    vector<int> next = getNext(t);
    int i = 0, j = 0;
    while (i < s.size() && j < t.size()) {
        if (s[i] == t[j]) {
            ++i;
            ++j;
        } else if (next[j] == -1) {
            ++i;
        } else {
            j = next[j];
        }
    }
    if (j == t.size())
        return i - j;
    else
        return -1;
}
```

## 改进KMP算法
主要改进了Next数组。

```c
void next_compute(char T[], int* next) {
    int i = 0, j = -1;
    next[0] = -1;
    while (i < strlen(T)) {
        if (-1 == j || T[i] == T[j]) {   // 自匹配
            i++;
            j++;
            if (T[i] != T[j])
                next[i] = j;
            else
                next[i] = next[j];
        }
        else {
            j = next[j];  // 字符不同，j值回溯
        }
    }
}
```

## Reference
[1:28:00开始](https://www.bilibili.com/video/BV13g41157hK?p=13)  
[](https://www.zhihu.com/question/21923021)