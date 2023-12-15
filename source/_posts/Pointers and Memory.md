---
title: Pointers and Memory
url: pointers-and-memory
date: 2020-01-01 23:07:00
description: pointer & memory
categories: Computer Science
tags: [Language]
---

*Stanford CS Education Library #102*
## Basic Pointers
指针主要有两个用途：使不同的代码段共享信息、方便链表（树）的处理。  
指针示意图：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019041619200938.png)  
`dereference`操作会根据指针的值去找到它的`pointee`。  
`NULL`是一个特殊的指针值（一般是地址0），表示这个指针不指向任何`pointee`。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416193109659.png)  
指针的赋值会使得两个指针指向相同的`pointee`，但`pointee`本身不会改变：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416192708547.png)  
传指针vs传值：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416193445969.png)  
定义一个指针后，这个指针是没有被初始化的：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019041619372662.png)  
这时候如果进行`dereference`操作会发生Runtime Error.  
对于Java、LISP等语言，当定义一个指针时，系统会将其设置为`NULL`，并且会在`dereference`操作时检查其值，这也是Java比较慢的原因之一。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416194404553.png)  
一个比较典型的指针错误：

```c
void BadPointer() {
	int* p;  //allocate the pointer, but not the pointee
	*p = 42; //serious RE
}
```
当执行`*p`时：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416195104100.png)

## Local Memory
函数开始运行时，会为局部变量分配内存，结束运行会回收内存。  
看一个错误的例子：

```c
//TAB -- The Ampersand Bug function
int* TAB() {
	int temp;
	return (&temp);
}

void Victim() {
	int* ptr;
	ptr = TAB();
	*ptr = 42;    //The pointee was local to TAB
}
```
问题在于`TAB()`返回了一个局部变量的地址，但这个局部变量的空间已经被回收，`ptr`指针没有`pointee`。

## Reference Parameters
传值：

```c
void B(int worth) {
	worth++;
	// T2
}

void A() {
	int netWorth = 55;  //T1
	B(netWorth);
	// T3 -- B() did not change netWorth
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416203356234.png)  
传指针：

```c
void B(int* worthRef) {
	(*worthRef)++;
	// T2
}

void A() {
	int netWorth = 55;  //T1
	B(&netWorth);
	// T3
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416203939385.png)  
传指针在c++中可以通过**传引用**的方式实现：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416205349526.png)

## Heap Memory
分配示意图：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416205821650.png)  
释放示意图：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416210059909.png)  
释放后，指针虽然还在，但却不可以在使用。

```c
//size以字节为单位，分配成功返回指针，失败返回NULL
void* malloc(unsigned long size);

//不需要size，因为heap manager之前已经记录过
void free(void* heapBlockPointer);
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416211219354.png)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416211242340.png)  
一个`StringCopy()`的例子：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416211900707.png)  
对于分配的堆内存，只有一个负责释放的，要么是`caller`，要么是`callee`：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190416212610387.png)