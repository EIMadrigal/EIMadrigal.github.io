---
title: Process Synchronization-Example 2
url: process-synchronization-example-2
date: 2018-04-20 20:59:32
description: 理发师睡觉问题
categories: Computer Science
tags: [OS]
---

## 问题描述
理发店有一位理发师，一把理发椅和N把供等候的顾客坐的椅子。  
如果没有顾客，理发师在理发椅上睡觉；  
当有一个顾客到来时，他必须先唤醒理发师；  
如果顾客来时理发师正在理发，如果有空椅子，坐下等待，否则离开。  
用PV操作解决上述问题中的同步和互斥关系。

## 分析
将顾客看作N个生产者，理发师是1个消费者。  
理发师和椅子是临界资源，故顾客间是互斥关系；  
理发师和顾客是同步关系。

信号量设置：
```c
Semaphore barberReady = 0  互斥量，只能取0或1  
Semaphore accessSeat = 1  互斥量，如果为1，表明椅子数可以增加或减少，相当于给椅子加锁，避免两个顾客同时坐一把椅子
Semaphore num_wait = 0   坐在椅子上等待的顾客数
int seat_free    空着的椅子数目
```
[参考wiki](https://en.wikipedia.org/wiki/Sleeping_barber_problem)

## 解答
```c
/*顾客进程*/
void customer()
{
    while(true)
    {
        P(accessSeat);  //试图坐下
        if(seat_free > 0)
        {
            seat_free--;  //坐下
            V(num_wait);  //试图唤醒理发师，
            V(accessSeat);  //不用再锁着椅子
            P(baberReady); //等待理发师ready
            理发;
        }
        else
        {
            V(accessSeat);  //释放加在椅子上的锁
            离开;
        }
    }
}

/*理发师进程*/
void barber()
{
    while(true)
    {
        P(num_wait);   //尝试获得一位顾客，如果没有，去睡觉
        P(accessSeat);   //尝试获得椅子锁，更改空闲椅子数目
        seat_free++;    //空椅子加1
        V(baberReady);    //理发师准备好了
        V(accessSeat);    //无需继续锁着椅子
        理发;
    }
}
```
