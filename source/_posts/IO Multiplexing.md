---
title: Linux的IO模型
url: io-multiplexing
date: 2020-10-23 18:28:00
description: Linux五种I/O模型
categories: Computer Science
tags: [Network]
---

## Blocking I/O
简单易用，本地I/O性能很高。处理网络I/O会造成进程阻塞空等，浪费资源。

## Non-Blocking I/O
kernel在数据未就绪时直接返回，增加system call的次数，消耗CPU资源。

## I/O Multiplexing

当多个独立的I/O事件同时发生时，I/O多路复用是一种解决方式。  
为了提高服务器的吞吐量，**单个线程**通过记录跟踪每个I/O流的状态同时管理多个I/O流，非常类似时分复用技术。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200825092905568.gif#pic_center)  
I/O多路复用的具体实现方式有3种：`select()`、`poll()`和`epoll()`。

### select
`select()`系统调用：`int select(int nfds, fd_set *readfds, fd_set *writefds, fd_set *exceptfds, struct timeval *timeout);`  
`select()`会一直阻塞直到至少一个文件描述符就绪，可以读写，或者出现异常。  
中间3个参数会被修改，表示哪个FD准备好了，最后一个表示等待时间，NULL表示无限等待。  
返回就绪的FD数目，有错-1。只知道有就绪，不知道哪个FD就绪了。
```c
int s = socket();
bind();
listen();

int fd[];  // 需要监听的socket集合
while (1) {
	int n = select();
	for (int i = 0; i < fd.size(); ++i) {
		if (FD_ISSET()) {  // 判断哪个socket接收到数据
			// 处理数据
		}
	}
}
```
`select()`是阻塞方法，只有某个socket接收到数据才会继续执行，唤醒进程。  
直接的方式缺点就比较多：

 - 时间开销大，所以规定最多监听1024个socket；
 - 每次调用都要把fd集合从用户态拷贝到内核态。
 - 线程不安全：

> If a file descriptor being monitored by select() is closed in another thread, the result is unspecified.

### poll
去掉了1024的限制，线程不安全。

### epoll
线程安全，知道哪个FD就绪，只有Linux支持。  
相比于`select()`，`epoll()`不会无差别轮询，只处理接收到数据的socket，这样复杂度就降低为$O(k)$。

水平触发：
边缘触发：读只要状态变化就会通知，写只要从满到非满就通知

## Signal-Driven I/O


## Asynchronous I/O


## Reference
[I/O多路复用是什么意思](https://www.zhihu.com/question/32163005/answer/55772739)  
https://zhuanlan.zhihu.com/p/358208161  
https://www.zhihu.com/question/59975081/answer/1932776593
https://wiyi.org/linux-io-model.html