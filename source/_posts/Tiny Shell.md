---
title: Tiny Shell
url: tiny-shell
date: 2020-08-02 16:30:00
description: A Simple Shell
categories: Computer Science
tags: [Projects]
---

## Introduction
本项目要实现一个简易版Shell，支持以下Features：
 - 命令提示符`tsh> `
 - 若用户输入的命令第一个单词是内置命令，在当前进程tsh(Tiny Shell)执行命令：
```bash
jobs   列出运行/挂起的后台job
bg <job>   通过发送SIGCONT信号重启进程，将后台挂起的job设为在后台运行，<job>可以是PID或%JID
fg <job>   通过发送SIGCONT信号重启进程，将后台运行/挂起的job设为在前台运行，<job>可以是PID或%JID
kill <job>   结束<job>
quit   退出tsh
```
 - 若用户输入的命令第一个单词是可执行文件路径，后续单词是命令行参数，tsh会fork一个子进程，在子进程运行
 - 支持job control（前后台切换），改变进程状态（running/stopped/terminated）
 - 支持管道`|`和I/O重定向`<` `>`(TODO)

## Background Knowledge

 - Shell  
shell是一个交互式的命令行解释器，可以执行用户输入的指令，显示计算结果。  
用户输入既可以是内置命令，也可以是可执行文件路径。  
用户既可以在前台运行，也可以在后台运行。后台job可以在命令最后加一个`&`，否则视为前台。比如`/bin/ls -l -d &`表示在后台执行`ls`程序。前台job只能有1个，后台job则可以有多个。
 - Signals  
信号机制的作用就是允许进程/内核打断其他进程运行，是进行进程间通信的一种方式。  
Linux的常用信号有：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200719145450179.png)

## Implementation
整体的思路是：父进程读取命令行输入，通过系统调用`fork()`创建子进程执行这些输入。对于前台命令，父进程必须等待子进程完成才能继续读取下一条命令；对于后台命令，父进程和子进程则是并发执行。



 - useful functions  
`int fork(void)`：父进程创建一个子进程，在子进程中返回0，父进程中返回子进程的PID。  
`int kill(pid_t pid, int sig)`：进程向其它进程（包括自己）发送信号，成功返回0，错误返回-1。可以通过改变参数`pid`调整发送目标：`pid>0`，给该进程发信号；`pid==0`，给包括自己在内的当前进程组发信号；`pid<0`，给pgid=|pid|的进程组的每个进程发信号。  
`int execve(char *filename, char *argv[], char *envp[])`：在当前进程的上下文环境中装载并运行新的程序，成功不返回，失败返回-1。`filename`可以是可执行目标文件或脚本文件，会覆盖原进程的data/code/stack，会保留原进程的PID/open files/signal context。  
`pid_t waitpid(pid_t pid, int *status, int options)`：指定进程终止父进程会进行回收，否则等待。内核会将子进程的退出状态传给父进程，之后清除子进程。`options=WNOHANG | WUNTRACED`时，如果wait set中没有终止/暂停的子进程，立即返回0；否则返回任意一个子进程的PID。  
`int sigprocmask(int how, const sigset_t *set, sigset_t *oldset)`：`how=SIG_BLOCK`将`set`中的信号加入阻塞向量；`how=SIG_UNBLOCK`将`set`中的信号从阻塞向量中移除；`how=SIG_SETMASK`将阻塞向量设置为`set`。如果`oldset!=NULL`之前的阻塞向量就存储在`oldset`中。  
`handler_t *signal(int signum, handler_t *handler)`：改变信号的默认行为。
 - step by step  
-- 整体结构：除了测试文件和Makefile外，全部实现都在`tsh.c`中，`main`函数有一个死循环，不停调用`eval()`实现命令的解析、执行。  
-- `eval(char* cmdline)`  
接收到用户输入后，第一件事就是解析。解析是通过`int parseline(const char* cmdline, char** argv)`完成，将`cmdline`解析到`argv`中，如果用户要求后台运行就返回1，否则返回0。  
解析后，我们需要通过`int builtin_cmd(char** argv)`判断是否为内置命令。如果是内置命令，就在`builtin_cmd`里立刻执行；否则需要创建子进程执行，这里需要区分前后台进程：如果是前台，需要等待terminate才能返回并接受新的输入；如果是后台，则可以立即接收新输入。
 - key point 1  
父进程在`fork`子进程之前，要用`sigprocmask`阻塞`SIGCHLD`信号。否则由于父子进程执行顺序不确定，可能导致：  
子进程首先执行完毕，内核向父进程发送`SIGCHLD`信号；  
从内核态切换到用户态时，检测到`SIGCHLD`信号并且执行`sigchld_handler`，删除该job；  
父进程执行`addjob`操作，显然删除和添加顺序反了。  
如果我们正确阻塞了`SIGCHLD`信号，还是按照上面的顺序：    
子进程首先执行完毕，内核向父进程发送`SIGCHLD`信号；  
从内核态切换到用户态时，由于父进程阻塞了`SIGCHLD`信号，所以不会执行`sigchld_handler`；  
父进程添加该job，解除`SIGCHLD`信号的阻塞，下一次context switch时删除job。
 - key point 2  
用户从键盘输入ctrl-c时，内核给shell进程发`SIGINT`信号（默认的signal handler是终止shell进程），一般有以下几种方式处理：

 1. 忽略该信号
 2. 使用默认的signal handler
 3. 实现一个单独的signal-handling函数

在`main`里面安装handler，在`sigint_handler`中处理：终止所有的前台进程及其子进程；  
用户从键盘输入ctrl-z时，内核给shell发`SIGTSTP`信号（默认暂停当前进程直到收到`SIGCONT`），在`main`里面安装handler，在`sigtstp_handler`中处理：暂停所有的前台进程及其子进程。
 - key point 3  
默认情况下，`fork`出来的子进程和他爹属于同一个进程组。当我们在机器上运行Tiny Shell时，程序运行在前台进程组中，这时如果Tiny Shell创建一些子进程，这些子进程也会同属于这个前台进程组，用户输入ctrl-c会终止所有前台进程，包括Tiny Shell，这显然不是我们想要的。  
解决方案是：`fork`之后，子进程调用`setpgid(0,0)`将其放到一个新的进程组里，这个组的group id和PID相同。这样就可以确保前台进程组里只有Tiny Shell一个进程，用户输入ctrl-c时，就可以在`sigint_handler`中调用`kill()`终止特定的前台job。
 - key point 4  
当子进程终止或者暂停，内核会给父进程发送`SIGCHLD`信号，我们在`sigchld_handler`中根据子进程的状态做相应的处理：  
`WIFEXITED(status)`：子进程通过`exit`或`return`正常终止；  
`WIFSIGNALED(status)`：子进程通过信号终止；  
`WIFSTOPPED(status)`：子进程暂停；  
`WTERMSIG(status)`：当`WIFSIGNALED()`为真，返回造成子进程终止的信号ID；  
`WSTOPSIG(status)`：当`WIFSTOPPED()`为真，返回造成子进程暂停的信号ID。
 - key point 5  
对于前台进程，需要一直等待其执行完毕，然后回收，可以在`waitfg()`中调用`sigsuspend`完成；但对于后台进程，由于不用等待其完成，所以为了避免其成为zombie，需要在其执行完毕或者暂停时通知父进程，这个机制就是signal，具体的就是我们的`sigchld_handler`做的事情。

如果子进程因为收到了未能捕获的信号(如SIGKILL)而终止，父进程尚未回收，子进程残留资源(PCB)存放于内核中，那么该子进程就成为僵尸进程。

处理僵尸进程有2种方法：
 - 杀死其父进程，子进程变为孤儿进程，进而被系统回收
 - `wait`函数

## Test
一方面通过提供的脚本测试，共有16个脚本测试文件，测试通过`make test01`~`make test16`进行；  
另一方面通过实际执行去测试各项功能。

## Code & Reference
[code here](https://github.com/EIMadrigal/15-213/tree/master/Tiny%20Shell)  
[reference here](http://csapp.cs.cmu.edu/3e/labs.html)

## TODO
 - IO重定向  
   https://www.cnblogs.com/weidagang2046/p/io-redirection.html
 - 管道  
   https://panqiincs.me/2017/04/19/write-a-shell-redirect-and-pipeline/
 - [xv6-riscv/sh.c](https://github.com/mit-pdos/xv6-riscv/blob/riscv//user/sh.c#L1)
 - history：支持快速访问最近的若干条命令
