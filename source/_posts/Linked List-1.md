---
title: Linked List-1
url: linked-list-1
date: 2020-01-01 23:01:00
description: 终结链表
categories: Computer Science
tags: [Algorithm]
---

链表一直是面试的重点问题，恰好最近看到了Stanford的一篇[材料](http://cslibrary.stanford.edu/)，涵盖了链表的基础知识以及派生的各种问题。  
第一篇主要是关于链表的基础知识。

## 基本结构
**1、数组回顾**  
链表和数组都是用来存储一堆数据的集合，其中单个元素的类型可以有很多种。  
通过数组下标可以直接访问数组中的元素，比如：

```
void ArrayTest()
{
	int scores[100];

	//初始化前3个元素
	scores[0] = 1;
	scores[1] = 2;
	scores[2] = 3;
}
```
最关键的是：整个数组被分配了一整块内存：  
![](https://img-blog.csdnimg.cn/2019032615150057.png)  
数组元素之所以能被快速访问，原因在于其地址的计算是通过首地址加上偏移值得到的，只有一次乘法和一次加法运算而已。  
数组的缺点在于：

 - 数组的大小是固定的：数组的规模在编译时就被确定，当然你可以在运行时通过`malloc`在堆中改变数组的大小，不过很麻烦；
 - 由于上述原因，所以很多人就会定义一个很大的数组，不过这又会导致两个问题：  
1）数组的大部分空间可能被浪费掉；  
2）如果程序需要更大的空间，就会崩溃。
 - 在数组前面插入元素代价很大，需要移动很多元素。  
链表也有自己的优缺点，只不过和数组刚好互补：链表会在需要时为每个节点单独分配内存。  
**2、指针回顾**  
指针存储了变量的地址，如果指针的值是`NULL`（c/c++中`NULL`可以表示逻辑`false`），那么该指针不指向任何变量。  
在c/c++中，没有初始化的指针就是野指针，对野指针进行`dereference`操作可能导致程序崩溃。  
两个指针的赋值结果就是都指向相同的内存区域。  
`malloc()`函数用来在堆中申请一块内存，并且返回一个指向该块的指针，如果申请失败，会返回`NULL`，使用后，需要用`free()`去释放。这些堆函数原型都在`stdlib.h`头文件中声明。  
**3、链表**  
一个包含`{1,2,3}`三个元素的链表：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190326161911717.png)  
空链表的`head`的值是`NULL`，**编程时要考虑到这种边界情况**。

节点的定义：

```
struct node {
	int data;
	struct node* next;
};
```
指向节点的指针类型是`struct node*`。  
接着看看上图中的链表是怎么建立的？

```
/*
Build the list {1,2,3} in the heap and store
its head pointer in a local stack variable.
Returns the head pointer to the caller.
*/
struct node* BuildOneTwoThree()
{
	//there are three pointers in the stack, but pointer assignment link the list.
	struct node* head = NULL;
	struct node* second = NULL;
	struct node* third = NULL;

	//allocate 3 nodes
	head = (struct node*)malloc(sizeof(struct node));
	second = (struct node*)malloc(sizeof(struct node));
	third = (struct node*)malloc(sizeof(struct node));

	head->data = 1;    //setup first node
	head->next = second;   //note:pointer assignment rule

	second->data = 2;    //setup second node
	second->next = third;

	third->data = 3;    //setup third node
	third->next = NULL;

	//at this point, the linked list referenced by "head"
	//matches the list in the drawing.
	return head;
}
```
如何求链表中的元素个数呢？

```
/*
Given a linked list head pointer, compute 
and return the number of nodes in the list.
*/
int Length(struct node* head)
{
	struct node* current = head;
	int count = 0;

	while (current != NULL)
	{
		count++;
		current = current->next;
	}

	return count;
}
```
可以看到，传递进函数的只是头指针，这样调用者和被调用者都有了头指针，但是却共享了整个链表。

 - `current`指针占据的空间会被自动释放，但是堆中的链表仍然保留；
 - `while`循环已经考虑了空链表的情况；
 - `current`最后的值会是`NULL`。  
调用`Length()`：

```
void LengthTest()
{
	struct node* myList = BuildOneTwoThree();
	int len = Length(myList);    //results in len == 3
}
```

 - 调用`Length()`之前：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190326172043909.png)
 - 执行`Length()`过程中：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190326172354519.png)

## 链表建立
用`BuildOneTwoThree()`函数来建立链表未免有些古板，下面用头插法建立链表：

1、分配节点：

```
struct node* newNode;
newNode = (struct node*)malloc(sizeof(struct node));
newNode->data = data_client_wants_stored;
```
2、让新节点的`next`指向当前链表的第一个节点：

```
newNode->next = head;
```
3、让`head`指针指向链表的第一个节点：
```
head = newNode;
```
整理下：

```
void LinkTest()
{
	struct node* head = buildTwoThree();  //suppose this builds list {2,3}
	struct node* newNode;

	newNode = (struct node*)malloc(sizeof(struct node));  //allocate
	newNode->data = 1;
	
	newNode->next = head;   //link next

	head = newNode;   //link head

	//now head points to the list {1,2,3}
}
```
如图： 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190326205401503.png)  
*先看一个错误的示范：*
```
void WrongPush(struct node* head,int data)
{
	struct node* newNode = (struct node*)malloc(sizeof(struct node));

	newNode->data = data;
	newNode->next = head;
	head = newNode;    //NO this line does not work
}

void WrongPushTest()
{
	struct node* head = buildTwoThree();  
	
	WrongPush(head, 1);    //try to push 1 on front -- doesn't work
}
```
这个问题就在于C语言的**值传递**，在`WrongPush()`中对`head`指针的改变不会影响到`WrongPushTest`中的我们需要的`head`指针。  
这个问题传统的解决方案是传递当前值的指针给函数而不是传递一份当前值的拷贝，即：  
要改变调用者中`int`的值，就传一个`int*`给被调用者。在这个例子中，要改变`struct node*`，就要传递`struct node**`。也即：`head`的类型是`pointer to a struct node`，想要改变这个指针，就需要传一个指向该指针的指针`pointer to a pointer to a struct node`。  
**规则就是：`to modify caller memory, pass a pointer to that memory.`**

*所以正确的代码如下：*
```
/*
Takes a list and a data value.
Creates a new link with the given data and pushes it
onto the front of the list.
The list is not passed in by its head pointer.
Instead the list is passed in as a "reference" pointer
to the head pointer -- this allows us to modify the caller's memory.
*/
void Push(struct node** headRef,int data)
{
	struct node* newNode = (struct node*)malloc(sizeof(struct node));

	newNode->data = data;
	newNode->next = *headRef;	//the * to dereferences back to the real head 
	*headRef = newNode;    //ditto
}

void PushTest()
{
	struct node* head = buildTwoThree();	//suppose this returns the list {2,3}
	
	Push(&head, 1);    //note the &
	Push(&head, 13);

	//head is now the list {13,1,2,3}
}
```
内存示意图：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190330143627528.png)  
如果是C++，那么可以用**引用**完成上述工作。

```
/*
Push in C++ -- We just add a & to the right hand side of the head parameter type,
and the compiler makes that parameter work by reference. So this code changes the 
caller's memory, but no extra uses of * are necessary -- we just access "head" directly,
and the compiler makes that change reference back to the caller.
*/
void Push(struct node*& head,int data)
{
	struct node* newNode = (struct node*)malloc(sizeof(struct node));

	newNode->data = data;
	newNode->next = head;	//No extra use of * necessary on head -- the compiler
	head = newNode;    //just takes care of it behind the scenes.
}

void PushTest()
{
	struct node* head = buildTwoThree();	//suppose this returns the list {2,3}
	
	Push(head, 1);    //No extra use & necessary -- the compiler 
	Push(head, 13);   //takes care of it here too. Head is being changed by these calls. 

	//head is now the list {13,1,2,3}
}
```