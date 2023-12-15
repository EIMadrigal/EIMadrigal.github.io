---
title: Process Synchronization-Example 1
url: process-synchronization-example-1
date: 2018-04-11 17:16:32
description: 考试问题
categories: Computer Science
tags: [OS]
---

## 问题描述
把学生和监考老师都看作进程，学生有N人，教师1人。考场门口每次只能进出一个人，进考场原则是先来先进。当N个学生都进入考场后，教师才能发卷子。学生交卷后可以离开考场，教师要等收上来全部卷子并封装卷子后才能离开考场。问：

- 需要设置几个进程？
- 用PV操作解决上述问题的同步互斥关系。

## 分析

> 考场门口每次只能进出一个人

考场门口是共享资源。

> 当N个学生都进入考场后，教师才能发卷子  
> 教师要等收上来全部卷子并封装卷子后才能离开考场

这是两个同步行为。

信号量设置：
```c
door = 1    //能否进出门口
mutex1 = 1
mutex2 = 1   //互斥信号量
sr = 0   //学生是否到齐
eb = 0    //考试开始
eo = 0     //考试结束

int num_stu = 0;
int num_paper = 0;
```

## 解答
```c
/*学生进程*/
void student()
{
    P(door);
    进门;
    V(door);
    P(mutex1);  //增加学生人数
    num_stu++;
    if(num_stu == N)
    V(sr);
    V(mutex1);
    P(eb);  //等教师宣布开始考试
    考试;
    交卷;
    P(mutex2);   //增加试卷份数
    num_paper++;
    if(num_paper == N)
    V(eo);
    V(mutex2);
    P(door);
    出门;
    V(door);
}

/*教师进程*/
void teacher()
{
    P(door);
    进门;
    V(door);
    P(sr);  //最后一个学生唤醒老师
    for(i = 1;i <= N;i++)
    发卷子;
    V(eb);     //开始考试
    P(eo);   //等待考试结束
    封装;
    P(door);
    出门;
    V(door);
}
```
