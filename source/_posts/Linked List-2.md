---
title: Linked List-2
url: linked-list-2
date: 2020-01-01 23:03:00
description: 终结链表
categories: Computer Science
tags: [Algorithm]
---

## 三、编码技巧
**1、遍历链表**  
先将`head`指针赋值给一个局部变量`current`：

```
//return the number of nodes in a list (while-loop version)
int Length(struct node* head)
{
	int count = 0;
	struct node* current = head;

	while (current != NULL)
	{
		count++;
		current = current->next;
	}

	return count;
}
```
当然也可以写为：

```
for (current = head; current != NULL; current = current->next) {}
```
**2、通过传递`reference pointer`改变某个指针**  
看个例子：

```
//Change the passed in head pointer to be NULL
//Uses a reference pointer to access the caller's memory
void ChangeToNull(struct node** headRef)  //takes a pointer to the value of interest
{
	*headRef = NULL;	//use * to access the value of interest
}

void ChangeCaller()
{
	struct node* head1;
	struct node* head2;

	ChangeToNull(&head1);	//use & to compute and pass a pointer to
	ChangeToNull(&head2);	//the value of interest
	//head1 and head2 are NULL at this point
}
```
这块的思想是和（一）中的`Push()`类似。  
内存示意图：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190330151133187.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0VJTWFkcmlnYWw=,size_16,color_FFFFFF,t_70)  
**3、通过`Push()`建立链表（头插法）**
这种方式的优点是速度飞快，简单易行，缺点是得到的链表是逆序的：

```
struct node* AddAtHead()
{
	struct node* head = NULL;

	for (int i = 1; i < 6; i++)
	{
		Push(&head, i);
	}

	//head == {5,4,3,2,1};
	return head;
}
```
**4、尾插法建立链表**
这种方法需要找到链表最后一个节点，改变其`.next`域：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190330155111506.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0VJTWFkcmlnYWw=,size_16,color_FFFFFF,t_70)

 - 插入或者删除节点，需要找到该节点的前一个节点的指针，改变其`.next`域；
 - 特例：**如果涉及第一个节点的操作，那么一定要改变`head`指针。**

 **5、特例+尾插法**
 如果要构建一个新的链表，那么头节点就要单独处理：
 

```
struct node* BuildWithSpecialCase()
{
	struct node* head = NULL;
	struct node* tail;
	
	//deal with the head node here, and set the tail pointer
	Push(&head, 1);
	tail = head;

	//do all the other nodes using "tail"
	for (int i = 2; i < 6; i++)
	{
		Push(&(tail->next), i);   //add node at tail->next
		tail = tail->next;     //advance tail to point to last node
	}

	return head;    //head == {1,2,3,4,5}
}
```
**6、临时节点建立**

```
struct node* BuildWithDummyNode()
{
	struct node dummy;   //dummy node is temporarily the first node
	struct node* tail = &dummy;   //build the list on dummy.next

	dummy.next = NULL;

	for (int i = 1; i < 6; i++)
	{
		Push(&(tail->next), i);
		tail = tail->next;
	}

	//the real result list is now in dummy.next
	//dummy.next == {1,2,3,4,5}
	return dummy.next;
}
```
**7、本地指针建立**

```
struct node* BuildWithLocalRef()
{
	struct node* head = NULL;
	struct node** lastPtrRef = &head;   //start out pointing to the head pointer

	for (int i = 1; i < 6; i++)
	{
		Push(lastPtrRef, i);  //add node at the last pointer in the list
		//advance to point to the new last pointer
		lastPtrRef = &((*lastPtrRef)->next);
	}

	return head;  //head == {1,2,3,4,5}
}
```
这块可能有些抽象：  
1）`lastPtrRef`开始指向`head`指针，以后指向链表最后一个节点中的`.next`域；  
2）在最后加上一个节点；  
3）让`lastPtrRef`指针向后移动，指向最后一个**节点的`.next`域**。    `(*lastPtrRef)->next`可以理解为`*lastPtrRef`指针指向的节点的`next`域。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190403193507385.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0VJTWFkcmlnYWw=,size_16,color_FFFFFF,t_70)

## 四、代码示例
**1、AppendNode()**
1) 不使用`Push()`函数：

```
struct node* AppendNode(struct node** headRef, int num)
{
	struct node* current = *headRef;
	struct node* newNode;

	newNode = (struct node*)malloc(sizeof(struct node));
	newNode->data = num;
	newNode->next = NULL;

	//special case for length 0
	if (current == NULL)
	{
		*headRef = newNode;
	}
	else
	{
		//Locate the last node
		while (current->next != NULL)
		{
			current = current->next;
		}

		current->next = newNode;
	}
}
```

2) 使用`Push()`函数：

```
struct node* AppendNode(struct node** headRef, int num)
{
	struct node* current = *headRef;

	//special case for length 0
	if (current == NULL)
	{
		Push(headRef, num);
	}
	else
	{
		//Locate the last node
		while (current->next != NULL)
		{
			current = current->next;
		}

		//Build the node after the last node
		Push(&(current->next), num);
	}
}
```
**2、CopyList**  
用一个指针遍历原来的链表，两个指针跟踪新生成的链表（一个`head`，一个`tail`）。

1) 不使用`Push()`函数：
```
struct node* CopyList(struct node* head)
{
	struct node* current = head;   //used to iterate over the original list
	struct node* newList = NULL;   //head of the new list
	struct node* tail = NULL;     //kept pointing to the last node in the new list

	while (current != NULL)
	{
		if (newList == NULL)    //special case for the first new node
		{
			newList = (struct node*)malloc(sizeof(struct node));
			newList->data = current->data;
			newList->next = NULL;
			tail = newList;
		}
		else
		{
			tail->next = (struct node*)malloc(sizeof(struct node));
			tail = tail->next;
			tail->data = current->data;
			tail->next = NULL;
		}
		current = current->next;
	}

	return newList;
}
```
内存示意图：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190407094454488.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0VJTWFkcmlnYWw=,size_16,color_FFFFFF,t_70)
2) 使用`Push()`函数：

```
struct node* CopyList2(struct node* head)
{
	struct node* current = head;   //used to iterate over the original list
	struct node* newList = NULL;   //head of the new list
	struct node* tail = NULL;     //kept pointing to the last node in the new list

	while (current != NULL)
	{
		if (newList == NULL)    //special case for the first new node
		{
			Push(&newList, current->data);
			tail = newList;
		}
		else
		{
			Push(&(tail->next), current->data);   //add each node at the tail 
			tail = tail->next;       //advance the tail to the new last node;
		}
		current = current->next;
	}

	return newList;
}
```
3) 使用`Dummy Node`：

```
struct node* CopyList3(struct node* head)
{
	struct node* current = head;   //used to iterate over the original list
	struct node* tail = NULL;     //kept pointing to the last node in the new list
	struct node dummy;            //build the new list off this dummy node

	dummy.next = NULL;
	tail = &dummy;      //start the tail pointing at the dummy

	while (current != NULL)
	{
		Push(&(tail->next), current->data);   //add each node at the tail
		tail = tail->next;                    //advance the tail to the new last node
		current = current->next;
	}

	return dummy.next;
}
```
4) 使用`Local References`：

```
struct node* CopyList4(struct node* head)
{
	struct node* current = head;   //used to iterate over the original list
	struct node* newList = NULL;   //head of the new list
	struct node** lastPtr;           

	lastPtr = &newList;      //start off pointing to the head itself

	while (current != NULL)
	{
		Push(lastPtr, current->data);   //add each node at the lastPtr
		lastPtr = &((*lastPtr)->next);    //advance lastPtr
		current = current->next;
	}

	return newList;
}
```
核心思想是使用`lastPtr`指针指向每个节点的`.next`域这个指针，而不是指向节点本身。

5. 使用`Recursive`：

```
struct node* CopyList5(struct node* head)
{
	struct node* current = head;
	if (head == NULL)
		return NULL;
	else {
		struct node* newList = (struct node*)malloc(sizeof(struct node));  //make one node
		newList->data = current->data;

		newList->next = CopyList5(current->next);    //recur for the rest
		return newList;
	}
}
```
