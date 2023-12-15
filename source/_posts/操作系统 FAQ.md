---
title: 操作系统 FAQ
url: os-faq
date: 2019-02-22 20:15:32
description: 操作系统八股
categories: Computer Science
tags: [OS,Interview]
---

## 进程

### 进程线程协程

进程拥有一个完整的资源平台，而线程只独享必不可少的资源，如寄存器和栈；  
线程同样具有就绪、阻塞、执行三种基本状态，同样具有状态之间的转换关系；  
线程能减少并发执行的时间和空间开销；

1. 联系: 线程存在于进程内部, 一个进程可以有多个线程, 一个线程只能属于一个进程.
2. 区别: 进程是运行时程序的封装, 是系统进行资源分配和资源调度（包括内存、打开的文件等）的基本单位; 线程是进程的子任务, 是CPU分配和调度的基本单位. 进程创建需要系统分配内存, CPU和文件句柄等资源, 销毁时要进行相应的回收, 因此进程的管理开销大; 线程开销小. 进程间不会互相影响; 一个线程崩溃会导致进程崩溃, 从而影响其他线程.

协程即微线程：协程就是子程序在执行时中断并转去执行别的子程序，在适当的时候又返回来执行。这种子程序间的跳转不是函数调用，也不是多线程执行，所以省去了线程切换的开销，效率很高，并且不需要多线程间的锁机制，不会发生变量写冲突。

协程进行中断跳转时将函数的上下文存放在其他位置中，而不是存放在函数堆栈里，当处理完其他事情跳转回来的时候，取回上下文继续执行原来的函数。

线程：频繁创建销毁，需要大量计算，切换频繁，IO密集型  
进程：稳定安全，CPU密集型

[协程由来和概念](https://zhuanlan.zhihu.com/p/204965836)  
[协程诞生](https://www.zhihu.com/question/50185085/answer/183463734)  
[线程和协程的区别](https://zhuanlan.zhihu.com/p/169426477)

### 通信方式
进程间通信：

- 管道
- 信号量
- 消息队列
- 信号
- 共享内存
- 套接字：[本地socket](https://www.cnblogs.com/vonyao/p/3614320.html)

线程间通信：

- 锁机制：互斥锁 + 条件变量，读写锁
- 全局变量：在各线程共享的堆上，每个线程私有栈
- 事件event：有信号和无信号两种状态
- Semaphore


互斥锁用来保证线程间的互斥，条件变量控制线程间执行顺序。比如生产者消费者中，缓冲区作为共享资源每次只能有一个线程访问，线程只有获取互斥锁才能访问，  
然而生产者访问并生产后，消费者并不知道何时缓冲区才有物品，故需要条件变量通知消费者，消费者无需忙等。  
假如只有mutex没有cv，那么可能有忙等；[isn't a mutex enough?](https://stackoverflow.com/questions/12551341/when-is-a-condition-variable-needed-isnt-a-mutex-enough)  
假如只有cv没有mutex，[Why condition variable require a mutex?](https://stackoverflow.com/questions/2763714/why-do-pthreads-condition-variable-functions-require-a-mutex)  
[条件变量之稀里糊涂的锁](https://zhuanlan.zhihu.com/p/55123862)

### Zombie & Orphan Process
孤儿进程：父进程先于子进程结束，内核控制其被init进程收养。并不是一种单独的状态。  
僵尸进程：子进程先结束但是父进程并未回收，使得残留资源PCB在内核中，不能用kill杀死，状态是Z。

处理僵尸进程方法：

1. 杀死其父进程，使其成为孤儿进程
2. 信号机制：子进程退出时向父进程发送SIGCHILD信号，父进程处理该信号，在处理函数中调用wait回收资源。

[Zombie & Orphan Process](https://www.scaler.com/topics/operating-system/zombie-and-orphan-process-in-os/)

### 进程调度算法

1. 先来先服务(FCFS): 按照到达任务队列的顺序调度, 非抢占式, 易于实现, 效率低性能差, 有利于CPU繁忙型作业(长作业)不利于IO繁忙型(短作业).
2. 短作业优先(SJF): 每次从任务队列选择预计时间最短的作业运行, 非抢占式, 性能最优, 平均周转时间最低, 吞吐量大, 不利于长作业, 会出现饥饿现象, 完全未考虑作业的优先级, 不能用于实时系统.
3. 最短剩余时间优先: 首先选择预计时间最短的作业运行, 如果新作业服务时间小于当前作业的剩余时间, 抢占CPU.
4. 高响应比优先: 在后备作业队列中选择响应比最高的, 非抢占式, 需要计算响应比耗费资源. $响应比=1+\frac{等待时间}{服务时间}$
5. 时间片轮转(RR): 可以响应所有用户的请求, 适于分时系统.
6. 多级反馈队列: UNIX使用的调度算法. 多个不同优先级的队列按照RR调度, 如果未完成就进入下一优先级, 新来进程可以根据优先级抢占.

## 死锁

1. 原因: (1) 系统资源不足; (2) 进程推进顺序不当; (3) 资源分配不当.
2. 必要条件: (1) 互斥访问: 一个资源每次只能被一个进程访问; (2) 占有并请求: 进程因请求资源阻塞时对已占有的资源保持不放; (3) 不可剥夺: 进程已经获取的资源不能被强制剥夺; (4) 循环等待: 多个进程间形成资源的循环等待关系.
3. 处理：资源按序编号分配，所有线程按照相同的顺序


## 文件系统

文件系统是操作系统中负责管理持久数据的子系统，说简单点，就是负责把用户的文件存到磁盘硬件中，因为即使计算机断电了，磁盘里的数据并不会丢失，所以可以持久化的保存文件。文件系统的种类众多，而操作系统希望对用户提供一个统一的接口，于是在用户层与文件系统层引入了中间层，这个中间层就称为虚拟文件系统（Virtual File System，VFS）。

https://zhuanlan.zhihu.com/p/183238194
https://zhuanlan.zhihu.com/p/106459445
https://zhuanlan.zhihu.com/p/27875337
https://www.zhihu.com/question/284540952
https://www.zhihu.com/question/51329419/answer/125389853

ext4
https://zhuanlan.zhihu.com/p/27875337


多线程访问某个方法，不论线程的执行顺序如何，都无需在主程序同步，最终的结果都是我们期望的，该方法线程安全

   
 1.   
   我举了计数器  
   [如何写出线程不安全的代码](https://zhuanlan.zhihu.com/p/32531445)
 2.   
   使用原子操作，加锁同步；可重入代码，thread-local存储，不可变对象，用volatile等避免过度优化  
   [Thread safety](https://en.wikipedia.org/wiki/Thread_safety), [确保线程安全的几种方法](https://developer.aliyun.com/article/254282)

   3.  [answer1](https://cs-fundamentals.com/tech-interview/c/difference-between-static-and-dynamic-linking) [answer2](https://stackoverflow.com/questions/311882)
   


 5.   
   并发和并行. [Multiple threads single-core CPU](https://softwareengineering.stackexchange.com/questions/176169)
 
 6. 
  - 开放定址法：线性探测，线性步长n探测，伪随机探测
  - 拉链法
  - 再哈希
  - 建立公共溢出区

 2.   
  [Zombie process vs Orphan process](https://stackoverflow.com/questions/20688982)
 4.   
  [How does valgrind work?](https://stackoverflow.com/questions/1656227/how-does-valgrind-work/)
 5.   
   [为什么内存对齐](https://www.pengrl.com/p/20020/)







bit数组直接用char a[pow(2,24)]吗？
拆分锁


假设限流阈值为1000QPS，每次访问都进行一次计数，超过的访问直接丢弃。1s结束后计数器清零，重新开始计数。  
  https://zhuanlan.zhihu.com/p/376564740







为了解决超出物理内存的大型程序的运行问题. 每个进程都有自己的地址空间, 按pages组织, 每个page都是一段连续的地址. 部分页面被映射到物理内存, 执行失败导致缺页中断.

TCP: 更加看重可靠性, 文件传输, 邮件, 浏览网页  
UDP: 更加看重速度, 视频会议, 实时音视频, 游戏 


在应用层实现ack和重传机制. [make udp reliable](https://networkengineering.stackexchange.com/questions/16809/how-to-make-udp-reliable)


 ```
    gcc main.c -Og main.exe 生成调试信息，-Og介于O0和O1之间，在保持快速编译和良好调试体验的同时，提供较为合理的优化级别
    gdb main.exe
    break linenum  // 源代码某行打断点
    run  // 执行程序直到第一个断点
    continue  // 继续执行直到下一个断点或程序结束
    next  // 逐行执行
    print variable  // 打印变量值
    list  // 显示源代码的内容
    quit 
    ```

## 事务

所有数据库的操作是不可分割的，要么全部执行成功，要么全部失败，不允许出现中间状态的数据。比如转账。

  1) Atomicity. 一个事务中的所有操作，要么全部完成，要么全部不完成，不会结束在中间某个环节，而且事务在执行过程中发生错误，会被回滚到事务开始前的状态，就像这个事务从来没有执行过一样。undo log（回滚日志）
  2) Consistency. 是指事务操作前和操作后，数据满足完整性约束，数据库保持一致性状态。持久性+原子性+隔离性
  3) Isolation. 数据库允许多个并发事务同时对其数据进行读写和修改的能力，隔离性可以防止多个事务并发执行时由于交叉执行而导致数据的不一致，因为多个事务同时使用相同的数据时，不会相互干扰，每个事务都有一个完整的数据空间，对其他并发事务是隔离的。MVCC（多版本并发控制） 或锁机制
  4) Durability. 事务处理结束后，对数据的修改就是永久的，即便系统故障也不会丢失。 redo log （重做日志）