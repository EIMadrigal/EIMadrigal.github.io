---
title: Performance Measurement
url: performance-measure
date: 2020-12-20 19:42:00
description: Profiling & Benchmarking
categories: Computer Science
tags: [Tools]
---

程序性能的衡量一般有2种方式：Benchmarking和Profiling。
## Benchmarking
Benchmarking通过绝对运行时间来比较程序的整体性能，例如给定一组同样的输入，比较不同版本的程序在同样硬件环境下的运行时间，或者比较相同版本程序在不同硬件环境下的运行时间。

## Profiling
Profiling通常用来识别程序的耗时瓶颈（通常是一些函数），优化这些瓶颈后再去做Benchmarking评估整体性能。  
Unix系统提供了GPROF工具，CSAPP上有一个示例：

 - 编译：`gcc -Og -pg prog.c -o prog`  
`-Og`表示关闭了很多编译器的优化开关，并且优化了调试信息；`-pg`表示产生供GPROF剖析用的可执行文件。
 - 执行：`./prog file.txt`  
会比正常执行要慢一些（慢一倍左右），生成待分析的文件`gmon.out`。
 - 分析：`gprof prog`

结果通常有2部分。  
第一部分是不同函数的耗时情况：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201012215440170.png#pic_center)  
第4列显示了该函数被调用了多少次（不含递归调用），库函数一般不显示在列表中，但是可以通过wrapper function去显示它的执行情况。  
第二部分是函数的调用情况，以递归函数`find_ele_rec`为例：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201012220111993.png)  
前2行显示了调用该函数的情况：自己递归调用了158555725次，被`insert_string`调用了965027次，`insert_string`自己递归调用了965027次；后几行显示了`find_ele_rec`调用其他函数的情况。

由于GPROF采用的是interval counting，所以计时可能不太精确，尤其对于运行时间小于1s的程序误差更大。  
如何使用Profiling去优化程序，CSAPP上给了一个非常精彩的例子，这里不再赘述。