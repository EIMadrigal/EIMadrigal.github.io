---
title: Linked List-3
url: linked-list-3
date: 2020-01-26 15:46:00
description: 终结链表
categories: Computer Science
tags: [Algorithm]
---

第一篇[终结Linked List（一）](https://www.cnblogs.com/EIMadrigal/p/12130882.html)、[终结Linked List（二）](https://www.cnblogs.com/EIMadrigal/p/12130892.html)主要讲了单链表的基础知识，接下来的第二篇主要讲一些比较经典的问题。

## 一、`Count()`
给一个单链表和一个整数，返回这个整数在链表中出现了多少次。

```
/*
Given a list and an int, return the number of times 
that int ocucurs in the list.
*/
int Count(struct node* head,int searchFor)
{
	int cnt = 0;
	struct node* cur = head;

	while (cur != NULL)
	{
		if (cur->data == searchFor)
			cnt++;
		cur = cur->next;
	}

	return cnt;
}
```
也可以用`for`循环实现。

## 二、`GetNth()`
给一个单链表和一个index，返回index位置上的数值，类似`array[index]`操作。

```
/*
Given a list and an index, return the data in the nth
node of the list. The nodes are numbered from 0.
Assert fails if the index is invalid (outside 0..length - 1).
*/
int GetNth(struct node* head,int index)
{
	int len = 0;
	struct node* cur = head;

	while (cur)
	{
		if (len == index)
		{
			return cur->data;
		}
		cur = cur->next;
		len++;
	}

	assert(0);  //如果走到这一行，表达式的值为假，断言失败
}
```

## 三、`DeleteList()`
给一个单链表，删除所有节点，使`head`为`NULL`。
删除链表`{1,2,3}`的示意图：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/201904071952350.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0VJTWFkcmlnYWw=,size_16,color_FFFFFF,t_70)
```
void DeleteList(struct node** headRef)
{
	struct node* cur = *headRef;  //deref headRef to get the real head

	while (*headRef)
	{
		cur = *headRef;
		*headRef = cur->next;
		free(cur);
	}
}
```

## 四、`Pop()`
给一个链表，删掉头节点，返回头节点的数据。  
内存示意图：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190407230157807.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0VJTWFkcmlnYWw=,size_16,color_FFFFFF,t_70)

```
/*
The opposite of Push().Takes a non-empty list and 
remove the front node, and returns the data which was in that node.
*/
int pop(struct node** headRef)
{
	assert(*headRef != NULL);
	int ans = (*headRef)->data;  //pull out the data before the node is deleted

	struct node* cur = *headRef;
	*headRef = (*headRef)->next;   //unlink the head node for the caller
	free(cur);    //free the head node

	return ans;
}
```

## 五、`InsertNth()`
可以在`[0,length]`的任意位置插入指定元素。

```
/*
A more general version of Push().
Given a list, an index 'n' in the range 0..length,
and a data element, add a new node to the list so that
it has the given index.
*/
void InsertNth(struct node** headRef,int index,int data)
{
	//position 0 is a special case
	if (index == 0)
	{
		Push(headRef, data);
	}
	else
	{
		int cnt = 0;
		struct node* cur = *headRef;

		while (cnt < index - 1)
		{
			assert(cur != NULL);   //if this fails, the index was too big
			cur = cur->next;
			cnt++;
		}

		assert(cur != NULL);    //tricky:you have to check one last time

		Push(&(cur->next), data);
	}
}
```
这段代码坑有点多，可以通过**画图**或者**单步跟踪**的方法调试。  
`InsertNthTest()`可以用来测试：

```
void InsertNthTest()
{
	struct node* head = NULL;   //start with the empty list
	 
	InsertNth(&head, 0, 13);   //{13}
	InsertNth(&head, 1, 42);    //{13,42}
	InsertNth(&head, 1, 5);     //{13,5,42}
}
```

## 六、`SortedInsert()`
给定一个有序链表和一个节点，将该节点插入到合适的位置。
共有三种方法：
1、Uses special case code for the head end
```
void SortedInsert(struct node** headRef,struct node* newNode)
{
	//Special case for the head end
	if (newNode->data <= (*headRef)->data || *headRef == NULL)
	{
		newNode->next = *headRef;
		*headRef = newNode;
	}
	else
	{
		//Locate the node before the point of insertion
		struct node* cur = *headRef;
		while (cur->next && cur->next->data < newNode->data)
		{
			cur = cur->next;
		}
		newNode->next = cur->next;
		cur->next = newNode;
	}
}
```
2、Dummy node strategy for the head end
用`dummy node`这种方法一般不需要处理特殊情况。

```
void SortedInsert2(struct node** headRef,struct node* newNode) {
	struct node dummy;
	struct node* cur = &dummy
	dummy.next = *headRef;

	while (cur->next && newNode->data >= cur->next->data)
	{
		cur = cur->next;
	}
	newNode->next = cur->next;
	cur->next = newNode;

	*headRef = dummy.next;  //头指针永远指向dummy.next
}
```
3、Local references strategy for the head end

```
void SortedInsert3(struct node** headRef,struct node* newNode)
{
	struct node** curRef = headRef;

	while (*curRef && (*curRef)->data <= newNode->data)
	{
		curRef = &((*curRef)->next);
	}

	newNode->next = *curRef;  //Bug:(*curRef)->next  is incorrect

	*curRef = newNode;
}
```

## 链表反转
链表反转的题目都是套路, 如果要反转$[l,r)$内的部分, 需要记录l的前一个结点. 让`cur = l, prev = r`, 循环次数等于$[l,r)$内的结点数, 每次循环都是标准操作:
```cpp
tmp = cur.next  
cur.next = prev  
prev = cur  
cur = tmp 
``` 

最终`cur`指向r, `prev`指向反转后的第一个结点即原链表的r - 1.  
头结点主要看你是反转整个链表还是一部分，反转整个链表就没必要了，因为l直接指向头结点  
92题可以直接按照上述方法做，但是开始要把r指向正确的位置，相当于多走了一遍，因此开始把prev设置为None，反转结束先通过l前一个结点修正l的指针指向cur，再让l前一个结点指向prev

25题因为要分组反转，所以需要判断剩余的结点够不够k个，因此r必然需要向后走一遍去判断，因此[l,r)肯定要走2遍。