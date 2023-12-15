---
title: Bit Manipulation
url: bit-manipulation
date: 2020-08-26 16:38:00
description: Bit操作
categories: Computer Science
tags: [Algorithm]
---

位操作可以使得我们细粒度地控制数据，但是很多技巧显得非常tricky，需要做一些总结。

## Basics
常见的操作有：与、或、非、异或和移位。

 - `n & (n - 1)`：将n的二进制表示中最低位的`1`改为`0`
 - `a ^ b = b ^ a`，`(a ^ b) ^ c = a ^ (b ^ c)`，`a ^ 0 = a`，`a ^ a = 0`
 - `n & (-n)`或`n & (~n + 1)`：lowbit操作，将最低位的1及后面的0代表的数字转为十进制
 - `&`只会递减/不变
 - `a = a | (1 << i)`  将a的第i位设为1

## Examples

 1. 计算二进制中`1`的个数

```c
int countOne(int n) {
	int cnt = 0;
	while (n) {
		n = n & (n - 1);
		++cnt;
	}
	return cnt;
}
```

 2. 判断整数是否为2的幂

```c
unsigned int v; // we want to see if v is a power of 2
bool f;         // the result goes here 

f = (v & (v - 1)) == 0;

f = v && !(v & (v - 1));  // v=0特判
```

 3. 整数相加

```c
class Solution {
public:
    int getSum(int a, int b) {
        // a^b: a+b without carry
        // a&b: the carry
        return b == 0 ? a : getSum(a ^ b, (unsigned int)(a & b) << 1);
    }
};
```

 4. 将n中第i~j位置0

```cpp
int mask = 0;
for (int k = i; k <= j; ++k) {
	mask |= (1 << k);
}
mask = ~mask;
n = n & mask;
```

## Reference
[A summary: how to use bit manipulation to solve problems easily and efficiently](https://leetcode.com/problems/sum-of-two-integers/discuss/84278/A-summary:-how-to-use-bit-manipulation-to-solve-problems-easily-and-efficiently)  
[Bit Twiddling Hacks](http://graphics.stanford.edu/~seander/bithacks.html)