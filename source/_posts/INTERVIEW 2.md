---
title: INTERVIEW 2
url: interview-2
date: 2019-03-28 16:46:00
description: SPD Bank
categories: Computer Science
tags: [Interview]
---
吐槽下ZZ的面试安排：面试时间12：30不说了，周围没有饭店，中午就没吃饭。。。不像其他公司给每个人安排不同的面试时间，这样可以节约大家的时间，SPDB是把一大批人都安排在了12：30，而且面试是5个面试官对一个人，生生地把可以并行的工作给整废了，大部分时间都浪费在了无意义的等待上。

## 机试
50min三道题，考察地很基础，基本之前都练过。利用的是[华科的OJ](http://hustoj.com/oj/)，IDE有Dev-C++、Eclipse、PyCharm，Dev-C++没太用过，所以调试得很慢很慢。。。

1. [十进制转二进制](http://acm.hdu.edu.cn/showproblem.php?pid=2051)
“除基取余，逆序排列”。每次将要转换的数除以基数Q，将余数作为**低位**存储直到商为0，将所有位由高到低输出即可。
```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <cstdio>

int main()
{
    int n;
    while (scanf("%d", &n) != EOF)
    {
        int len = 0, num[40];
        do {
            num[len++] = n % 2;
            n /= 2;
        } while (n);
        for (int i = len - 1; i >= 0; i--)
            printf("%d", num[i]);
        printf("\n");
    }

    return 0;
}
```

之所以用do...while循环，是因为如果输入为0，用while会直接跳出循环，结果出错。
2. 求出200以内所有3的倍数的数字和
没啥好说的。
```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <cstdio>

int main()
{
    int sum = 0;
    for (int i = 0; i < 200; i++)
    {
        if (!(i % 3))
        {
            sum += i;
        }
    }

    printf("%d\n", sum);

    return 0;
}
```
3. [质因子分解](https://pintia.cn/problem-sets/994805342720868352/problems/994805415005503488)
这题寒假练过，不过机考时候忘了，素数表打的好像有问题。。。幸亏测试数据弱，就手工写了一个数组存了前面20个素数，结果AC了。。。

- 如果一个正整数n是一个合数，那么它的因子必然是在$\sqrt n$左右两侧成对出现；
- 推广到质因子，如果n存在[2,n]内的质因子，那么这些质因子要么全部小于等于$\sqrt n$，要么只有一个大于$\sqrt n$，其余都小于等于$\sqrt n$。

所以算法是：
1）枚举1~$\sqrt n$内的所有质因子，判断其是否是n的因子；
2）如果1）结束后$n\geq 1$，那么其必然有且仅有一个大于$\sqrt n$的质因子，记录该因子；
3）输入是1要特判。
```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <cstdio>
#include <cmath>

const int maxn = 100000 + 10;

//如果是int范围，数组开10足够了，
//因为2*3*5*7*11*13*17*19*23*29就超过int了，所以我手工写一个数组也足够了。。。
struct fac {
    int x;
    int cnt;  //质因子x的个数
}fac[10];

bool isPrime(int a)
{
    if (1 == a)
        return false;

    int sqr = sqrt(1.0*a);
    for (int i = 2; i <= sqr; i++)
    {
        if (!(a % i))
            return false;
    }
    return true;
}

int prime[maxn], num = 0;
//打素数表
void primeTable()
{
    for (int i = 1; i < maxn; i++)
    {
        if (isPrime(i))
        {
            prime[num++] = i;
        }
    }
}

int main()
{
    primeTable();  //记得写，我好像没写这句。。。

    long long n;
    int diffFacNum = 0;  //n的不同质因子个数
    scanf("%lld", &n);
    printf("%lld=", n);

    if (1 == n)  //特判1
        printf("1");

    else
    {
        int sqr = sqrt(1.0*n);

        //枚举2~sqrt(n)的质因子
        for (int i = 0; prime[i] <= sqr; i++)
        {
            if (n % prime[i] == 0)  //如果该质因子是n的因子
            {
                fac[diffFacNum].x = prime[i];
                fac[diffFacNum].cnt = 0;
                //计算该质因子的个数
                while (n % prime[i] == 0)
                {
                    fac[diffFacNum].cnt++;
                    n /= prime[i];
                }
                diffFacNum++;
            }

            if (1 == n)
                break;
        }

        //必有一个大于sqrt(n)的质因子
        if (n != 1)
        {
            fac[diffFacNum].x = n;
            fac[diffFacNum++].cnt = 1;
        }

        for (int i = 0; i < diffFacNum; i++)
        {
            if (i > 0)
                printf("*");

            printf("%d", fac[i].x);
            if (fac[i].cnt > 1)
            {
                printf("^%d", fac[i].cnt);
            }
        }

    }

    return 0;
}
```

## 面试
面试期间也被问到了一道题：
大致意思就是有一个正整数n，找出一个比n大且每位数字之和=n的每位数字之和的最小数，比如输入050，那么输出104。
我开始的思路是从n开始向上枚举，直到找到满足要求的数；
其实更优的解法是：对于在050~099之间的数根本不用考虑，因为必然不满足每位数字之和=n的每位数字之和，这样可以提高效率。

## 其它
1、语言：Java多态、C的数据类型；
2、数据结构：链表是否有环（烂大街了）；
3、操作系统：进程状态及转换、进程线程区别。
</cmath></cstdio></cstdio></cstdio>