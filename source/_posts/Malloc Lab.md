---
title: Malloc Lab
url: malloc-lab
date: 2020-11-28 10:12:00
description: CSAPP
categories: Computer Science
tags: Projects
---

## Basic Info
这是CMU 15-213的Malloc Lab，本来没打算做，被同学安利了一波~  
需要用C语言实现一个动态内存分配器(Dynamic Storage Allocator)，类似于`Glibc`中的`malloc/free/realloc`, 由于涉及到很多未知类型的指针操作, 整体来看难度较大.

开始没什么思路，看了下CSAPP动态内存分配那一节，内存的划分是这样子的：
<img src="https://img-blog.csdnimg.cn/2020101116464245.png" alt="在这里插入图片描述" style="zoom:80%;" />
程序动态申请的内存主要是Heap段，Allocator将堆视作不同size的块，Allocator有2种：

 - Explicit Allocators：需要应用程序手动释放申请的内存块，`malloc/free`
 - Implicit Allocators：就是garbage collectors

`malloc`返回的对齐地址取决于编译环境，32位是8的倍数，64位是16的倍数；`malloc`不会初始化申请的内存，`calloc`会初始化内存为0。
堆的增长是通过增加内核的`brk`指针来增加/减小堆：
```c
void *sbrk(intptr_t incr);  // success: old brk pointer; error: -1
```
如果`free`的是一个非法指针，那么结果未定义。

主要实现在`mm.c`中，有4个函数：

```c
int mm_init(void);
void *mm_malloc(size_t size);
void mm_free(void *ptr);
void *mm_realloc(void *ptr, size_t size);
```

 - `mm_init`负责初始化, 比如分配初始的堆空间, 初始化自定义的数据结构. 成功返回0，失败返回-1;
 - `mm_malloc`负责分配指定payload大小的块，但是这个分配的块必须在已经`extend`的堆里, 且不能与其他已分配的块重叠; 返回指向该块payload的指针, 按照8B对齐, 与`libc`中实现的`malloc`类似, 最后会有一个PK；  
 - `mm_realloc`负责：
 - `ptr==NULL`等价于`void *mm_malloc(size)`
 - `size==0`等价于`mm_free(ptr)`
 - 如果`ptr`不为`NULL`，把`ptr`所指的内存块增加/减小到`size`个字节，返回新地址（可能与原来的块相同，也可能不同，取决于实现方式、原块里内碎片的数量以及请求`size`的大小），保持原块的内容不变（如果申请变大，剩下的空间是未初始化的；如果申请变小，那么只保留`size`大小的原块内容）

`memlib.c`为我们的分配器模拟了内存系统，可以调用里面的一些函数：  
`void *mem_sbrk(int incr)`: 将堆扩展`incr`字节，参数是正整数，返回新扩展的内存起始地址；  
`void *mem_heap_lo()`: 返回堆的首地址；  
`void *mem_heap_hi()`: 返回堆的最后一个字节的地址；  
`size_t mem_heapsize()`: 返回当前堆的大小；  
`size_t mem_pagesize()`: 返回系统页面大小。

## Implementation
首先明确设计约束条件：

 - 可以处理任意的请求释放序列
 - 请求需要被立即响应
 - 内存对齐要求
 - 不能修改已经分配的内存块

再明确设计目标：

 - 最大化吞吐量：平均每秒能够完成的操作次数
 - 最大化内存利用率：`mm_alloc`或`mm_realloc`申请但还未被释放的内存空间与堆大小的比值

内存利用率最大化：用peak utilization衡量，假设有$n$个请求：$R_0, R_1, ... R_k, ..., R_{n-1}$，在$R_k$完成后，将aggregate payload(申请的字节总数)记作$P_k$，当前的堆的大小记作$H_k$(单调不减)，单调不减的条件可以通过使$H_k$为high-water mark来松弛，这样堆就可以上下都增长。那么peak utilization为：
$$U_k=\frac{max_{i\leq k}P_i}{H_k}$$
我们的目标是最大化$U_{n-1}$。

这两目标是需要trade-off的，吞吐量越大，意味着需要提高速度，减小操作的时间，往往就不能花费时间去处理碎片，利用率下降；  
内存利用率越大，意味着要精心处理分配和回收的块，自然需要更多的时间，吞吐量下降。

具体来说，有以下几点：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201023215211206.png)
这些关键细节的设计非常重要，再BB一次：程序架构、数据结构和接口设计是一门艺术！

 - `free`只给一个指针，怎么知道要释放多少空间：记录每一块的大小；
 - **空闲块的组织管理**：隐式链表、显式（双向）空闲链表（存储指针域开销太大）、Segregated Free Lists
 - **Placement Policy**: 有多个满足要求的空闲块，如何选择以放置新的申请：First Fit, Next Fit, Best Fit
 - Splitting: 在一个空闲块放置申请后如何处理剩余的空闲空间。可以直接将整个空闲块分配出去，也可以将剩余的空闲空间重新利用；
 - 无法找到合适的满足请求的块：做空闲块合并后再次检查是否可以满足；用`sbrk`向内核申请更多的内存，插入空闲链表；
 - **空闲块合并**：需要考虑何时合并：立即合并（可能引发抖动）、延迟合并（申请失败时合并整个堆里所有的空闲块）；合并后面的块很容易，但是要高效合并前面的空闲块，需要用双向的Boundary Tag(可以优化以减少空间开销)；

不仅需要记录每块的大小，还需要区分已分配块和空闲块，所以block的格式可以设计如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201011195117307.png)
如果要求double-word(8B)对齐，那么Block size总是8的倍数，所以低3位都是0，可以利用其存储分配状态。

这样整个堆就可以组织为连续的分配和空闲块，由于已经存储了每块的大小，所以隐式空闲链表就应运而生：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201014165121101.png)

隐式空闲链表的优点就是实现简单，缺点就是当需要在所有的空闲块中查找时（如placement），需要扫描整个堆（包括已经分配的块）。显式链表的分配时间复杂度是$O(free)$，速度较慢但是如果采取best fit，内存利用率会比segregated free lists好一些。

为了降低隐式空闲链表合并的复杂度，Knuth大佬提出了boundary tags，这样实际上相当于隐式双向链表：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201011214226551.png)

这样就可以通过Footer在$O(1)$检查之前的块的状态及其开始位置，缺点在于如果小的内存块比较多的话，内存浪费太大。  
双向tag带来的内存开销可以优化：只有前面的块是空闲，才需要它footer里的大小，所以可以在每个块用后3位里的某一位来存储前面块的状态，那么已分配块就不需要footer了，可以把footer的空间用来当作payload，但是空闲块仍然需要2个tag。

那么现在整个堆变成了这样：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201016204303577.png)
这里的`heap_listp`指向Prologue block的中间是做了一些小优化，方便直接定位到下一块的数据位置。

这里的实现非常tricky和subtle，一开始只申请了4words共16B(unused(1)+Prologue(2)+Epilogue(1))，后续的`extend_heap`申请一个空闲块后，将原来的Epilogue作为空闲块的header，空闲块的最后一个word变为新的Epilogue。

由于对齐要求（Headers在非对齐位置，Payloads对齐），分配器应该有一个minimum block size，即使只请求了1B，也要分配minimum block size，这里是16B。

写代码时先实现并测试`malloc`和`free`，如果能正确并且高效执行，再去实现`realloc`。

## Evaluation
性能主要考虑2方面因素：

 - 空间利用率$U$：peak ratio即评测程序申请的总内存（`mm_malloc/mm_realloc`但是还没有`mm_free`）与分配器使用的堆容量的比值，需要用好的策略减小碎片，使得该值接近1；
 - 吞吐量$T$：每秒完成的操作数量

评测程序会综合考虑2个方面，计算一个performance index
$$P=wU+(1-w)min(1,\frac{T}{T_{libc}})$$
$T_{libc}$是`libc`中的`malloc`的吞吐量，大概在600Kops/s左右，$w=0.6$。
这个$P$既考虑了内存资源，又考虑了CPU资源，两个矛盾的指标需要适当权衡。

第一个版本`mm1.c`基本就是抄书，Implicit Free Lists+First Fit+Bi Boundary Tag，抄书也就将将及格。。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201115143013580.png)
将First-Fit改成Next-Fit，还不错：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201115202312126.png)
可以看到：Next-Fit在速度上有很大提升，主要是因为它是从上次终止的块开始搜索，避免了前面很多块的无效搜索。

最后对于Implicit Lists的性能做个总结：  
分配：$O(n)$  
释放：$O(1)$  
Memory Overhead：取决于First Fit等策略

感觉这个性能已经不错了，但是一些无聊的计算机科学家还是不满意分配时的效率。接着我们看下更加🐂🍺的方法Segregated Free Lists：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201024204031234.png)
每个size级别的块都有自己的free list，分配大小为n的块时：

 - 搜索合适的free list使得size>n
 - 找到：split并将remainder放入应该去的list
 - 未找到：向操作系统申请更大的内存，分出去n，将剩下的放入相应的list

释放时合并空闲块并且放入相应的free list即可。

这实际上近似模拟了Best Fit，而且不用搜索整个free list，Best Fit一般有着最优的内存利用率，但是运行时间$O(n)$，又是吞吐量和内存利用率的trade-off，终于明白了为什么官方的`malloc`要用这个方式了：吞吐量更大$O(lgn)$、更优的内存利用率Best Fit。

Segregated Free Lists中的空闲块包含Header+prev+next+padding+Footer，已分配块没有前后指针。
写代码要注意：整个堆中的块位置是不变的，只是状态（分配、释放）在改变，整个堆中的空闲块是用seg list的方式串起来的。

Debug可以自己写一下`mm_check`，还是很有用的。
`realloc`快de疯了，整整一个晚上。。。其实就4种情况：

 1. 如果当前已分配块后面是结尾块，直接申请新的堆空间，与原块组合返回；
 2. 如果当前已分配块后面是一个空闲块，且两者之和>=size，组合返回；
 3. 如果当前已分配块后是一个空闲块，但两者之和<size，但是空闲块后是结束块，申请新的堆空间，三者组合返回；
 4. malloc新块，将原块复制，释放原块。

第一次写完，只有85，内存利用率太差了：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201117220719669.png)
`place`的时候，如果申请块比较大，我们将其分配到后半部分，将前半部分切割为空闲：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020111909030584.png)
之前class的划分是1，2-3，4-7，8-15...
但是最小块是16B，所以16B以下的用不到，所以改为0-16B，17-32，...，257-512MB

这样优化后直接96：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201119092357832.png)
后面还可以继续优化榨干性能，比如去掉已分配块的Footer，`realloc`组合块以后对remainder进行split...  
以后有时间再说......

## 思考
显然上述实现只是toy example, 工业界涌现了很多优秀的内存分配器, 如glibc自带的ptmalloc, Google的tcmalloc, Facebook的jemalloc. 

## Reference
[tcmalloc/jemalloc](https://stackoverflow.com/questions/9866145)  
[对比分析](https://www.cyningsun.com/07-07-2018/memory-allocator-contrasts.html)