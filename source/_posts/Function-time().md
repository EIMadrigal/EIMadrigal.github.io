---
title: Function-time()
url: time-function
date: 2018-05-23 21:58:32
description: time()函数
categories: Computer Science
tags: [Language]
---

`time()`函数返回自1970年1月1日0点以来经过的秒数，每秒变化一次?  
`time()`函数定义在头文件`<time.h>`中，原型是：
```c
time_t time(time_t *arg);
```

如果`arg`不是空指针，那么函数返回`time_t`类型的calendar time，并且把结果保存在`arg`指向的对象；  
如果`arg == NULL`，那么函数只是返回一个值，值不能存储在空指针指向的对象。  
之前不明白为什么要设计一个参数`arg`，直接返回一个值就好了啊？  
有大神说，这是因为：

> 很久很久以前，据说`time_t`是个`struct`，那时候c语言不支持函数返回`struct`，所以只能用指针传进去。

那么`time_t`到底是什么类型呢？  
看看cppreference.com的定义：

> The encoding of calendar time in `time_t` is unspecified, but most systems conform to POSIX specification and return a value of integral type holding the number of seconds since the Epoch.Implementations in which `time_t` is a 32-bit signed interger(many historical implementations) fail in the year 2038.

就是说：C标准委员会并没有定义`time_t`的精度，也没有指定标准的Epoch，所以这取决于你的operating system以及你的compiler。  
如果你的系统支持**POSIX标准**(包括很多类Unix系统、Windows系统)，那么`time_t`是一个`signed int 32`，最大表示范围是2147483647秒，标准Epoch是1970年1月1日0点，所以最终时间就是2038年1月19日，这就是著名的[2038年问题](https://en.wikipedia.org/wiki/Year_2038_problem)。

那么这个函数的实现，GNU C Library是这么写的：
```c
#include <stddef.h>                /* For NULL.  */
#include <time.h>
#include <sys/time.h>
/* Return the current time as a `time_t' and also put it in *T if T is
   not NULL.  Time is represented as seconds from Jan 1 00:00:00 1970.  */
time_t
time (time_t *t)
{
  struct timeval tv;
  time_t result;
  if (__gettimeofday (&tv, (struct timezone *) NULL))
    result = (time_t) -1;
  else
    result = (time_t) tv.tv_sec;
  if (t != NULL)
    *t = result;
  return result;
}
libc_hidden_def (time)
```

如果返回`time_t`类型的值，说明调用成功；  
如果返回`(time_t)(-1)`，说明无法取得现在的时间，调用失败。  
举个栗子，获得当前时间：
```c
#include <stdio.h>
#include <time.h>
#include <stdlib.h>

int main(void)
{
    time_t current = time(NULL);
    char* string;

    /*把日期和时间转为字符串*/
    string = ctime(&current);
    if (current == (time_t)-1)
    {
        printf("Fail to get the current time!\n");
    }

    printf("The current time is %s", string);
    printf("(%d seconds since the Epoch)\n",current);
}
```

运行结果：
![image](1260581-20220108191119700-2067131670.png)
