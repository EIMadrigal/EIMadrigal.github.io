---
title: Random Number Generator
url: random-number-generator
date: 2018-05-24 22:07:32
description: 随机数生成器
categories: Computer Science
tags: [Language]
---

`rand()`函数可以产生[0,RAND_MAX]之间的均匀的**伪随机数**，它定义在头文件`stdlib.h`中，函数原型：
```c
int rand(void);
```

C标准库的实现是：
```c
unsigned long int next = 1;

/*rand: return pseudo-number integer on 0...32767*/
int rand(void)
{
    next = next*1103515245 + 12345;
    return (unsigned int)(next/65536) % 32768;
}

/*srand: set seed for rand()*/
void srand(unsigned int seed)
{
    next = seed;
}
```

如果没有初始化“随机数种子”，那么默认初始种子是1，1*1103515245+12345，return得到第一个伪随机数，接着将这个结果作为下次的种子，带入式子得到第二个伪随机数...  
之所以定义为`unsigned int`，是防止数值溢出后不会出现负值。  
直接调用`rand()`，会导致产生的是同一套随机数，所以我们使用`srand()`来初始化随机数种子。  
要注意的是：不同编译器计算随机数的方法不尽相同，所以即使给`srand()`传递相同的参数，也可能产生不同的随机数序列。  
举个栗子：
```c
/*产生0-9的随机数*/
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

int main(int argc,char** argv)
{
    srand(time(NULL));  //初始化随机数种子
    for(int i = 0;i < 5;i++)
    {
        printf("%d ",rand()%10);
    }

    return 0;
}
```

利用`rand()%n`产生[0,n)之间的随机数，那么一旦$n>RAND\_MAX$，这种做法就会失效。  
如果你对精度的要求不高，可以采用如下办法：  
先用`rand()/RAND_MAX`，得到[0,1]之间的随机实数，然后扩大n-1倍，四舍五入，就可得到[0,n-1]之间的随机数。
```c
/*产生10个[0,99999]之间的随机数*/
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

int main(int argc,char** argv)
{
    int n = 100000;
    double random_doub;
    int random_num;

    srand(time(NULL));  //初始化随机数种子

    for(int i = 0;i < 10;i++)
    {
        random_doub = (double)rand() / RAND_MAX; //生成[0,1]之间的随机数
        random_num = (int)((n - 1)*random_doub + 0.5); //生成[0,n-1]之间的随机数
        printf("%d ",random_num);
    }

    return 0;
}
```
![image](1260581-20220108194141397-884629663.png)
