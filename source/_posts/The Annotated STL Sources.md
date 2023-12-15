---
title: The Annotated STL Sources
url: the-annotated-stl-sources
date: 2020-07-03 10:02:00
description: STL源码剖析
categories: Computer Science
tags: [Language]
---

## Intro
《STL源码剖析》用来了解原理性的设计没什么问题，但是这本书实在太老，所有源码基于GNU2.9；现在语言的发展飞快，而且很多地方都是考虑兼容性等因素，设计非常复杂，也并不高效，我没有时间去搞明白所有实现，更没有时间实现标准库，所以只学了一小半就停了。

## 六大组件
容器、算法、分配器、迭代器、适配器、仿函数。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200423140448163.png)

## Allocator
分配器用来为容器分配内存，分配器是class，有成员函数`allocate` `deallocate`，调用`operator new()`会调用`malloc`，`operator delete()`调用`free`。  
不同编译器的分配器实现稍有区别，不建议直接使用allocators，`int* p = allocator<int>().allocate(512)` 会创建临时对象，归还还要指定大小：`allocator<int>().deallocate(p, 512)`。  
但`malloc`归还时不需要指定大小，因为`malloc`时候会有**cookie**保存分配的内存块大小，如果每次申请内存都包含cookie的话，开销太大，并且频繁申请内存十分耗时。  
GNU2.9觉得allocators太傻逼，自己用的是alloc的分配器，有16个单链表，每个链表负责某个特定大小的内存块分配，比如8B（该链表串了很多8B的小内存块），16B，...，容器需要内存会被调整到8的倍数，去相应的链表找，如果链表没有小块内存，就会调用`malloc`向OS申请一块大的，切成很多小的，串起来去分配，这样`malloc`次数会变小很多，而且cookie会少很多，时间和空间开销都会变小，碎片也少了。  
GNU4.9没有使用alloc，使用`std::allocator`，allocator继承了new_allocator，有成员函数`allocate` `deallocate`，调用`operator new()`会调用`malloc`，`operator delete()`调用`free`，一夜回到解放前。。。  
4.9有很多扩展的分配器，2.9里的alloc变为了_pool_alloc，要改变默认的分配器，可以写`vector<string, __gnu_cxx::_pool_alloc<string>> vec`。

## list
双向环状链表，end指向一个dummy node。  
因为非连续，所以`++iterator`要重新设计，使得指向下一个元素，而不是错误的地址。
```cpp
template<class T>
struct __list_node {
	typedef void* void_pointer;
	void_pointer prev; // 4.9 struct __list_node* prev
	void_pointer next; // 4.9 struct __list_node* next
	T data;
};

template<class T, class Ref, class Ptr>
struct __list_iterator {
	// 5种associated types
	typedef __list_iterator<T, Ref, Ptr> self;
	typedef bidirectional_iterator_tag iterator_category;
	typedef T value_type;
	typedef Ptr pointer; // 4.9 typedef T* pointer
	typedef Ref reference; // 4.9 typedef T& reference
	typedef __list_node<T>* link_type;
	typedef ptrdiff_t difference_type;

	link_type node;

	/* 操作符重载 */
	reference operator*() const { return (*node).data; }
	pointer operator->() const { return &(operator*()); }
	// 前置++
	self& operator++() {
		node = (link_type)((*node).next); // 指向下一个结点
		return *this;
	}
	// 后置++
	self operator++(int) {
		self tmp = *this; // 记录原值，拷贝构造
		++* this; // 操作
		return tmp; // 返回原值
	}
	...
};

template<class T, class Alloc = alloc>
class list {
protected:
	typedef __list_node<T> list_node;
public:
	typedef list_node* link_type;
	typedef __list_iterator<T, T&, T*> iterator;
	// typedef __List_iterator<_Tp> iterator; 4.9模板参数只有一个
protected:
	link_type node;
	...
};
```

## vector
1.5/2倍增长。  
迭代器只是一个指针，而不是class iterator，通过萃取机（Iterator Traits）中对类型的偏特化处理，可以回答算法提出的问题（iterator_category,value_type,difference_type,pointer,reference）
```cpp
template<class T, class Alloc>
void vector<T, Alloc>::insert_aux(iterator position, const T& x) {
	if (finish != end_of_storage) {
		construct(finish, *(finish - 1)); // 建立一个元素，并以最后一个元素作为初值
		++finish;
		T x_copy = x;
		copy_backward(position, finish - 2, finish - 1);
		*position = x_copy;
	}
	else {
		const size_type old_size = size();
		const size_type len = old_size != 0 ? 2 * old_size : 1;

		iterator new_start = data_alloctor::allocate(len);
		iterator new_finish = new_start;
		try {
			// 将原vector内容拷贝到新vector
			new_finish = uninitialized_copy(start, position, new_start);
			construct(new_finish, x); // 新元素设为x
			++new_finish;
			// 拷贝插入点后的元素，可能被insert调用
			new_finish = uninitialized_copy(position, finish, new_finish);
		}
		catch (...) {
			// commit or rollback
			destroy(new_start, new_finish);
			data_allocator::deallocate(new_start, len);
			throw;
		}
		destroy(begin(), end()); // 析构释放原vector
		deallocate();
		// 调整迭代器指向新的vector
		start = new_start;
		finish = new_finish;
		end_of_storage = new_start + len;
	}
}

template<class T, class Alloc = alloc>
class vector {
public:
	typedef T value_type;
	typedef value_type* iterator; // T*, just a pointer, not a class iterator
	typedef value_type& reference;
	typedef size_t size_type;
protected:
	iterator start;
	iterator finish;
	iterator end_of_storage;
public:
	iterator begin() { return start; }
	iterator end() { return finish; }
	size_type size() const { return size_type(end() - begin()); }
	size_type capacity() const { return size_type(end_of_storage - begin()); }
	bool empty() const { return begin() == end(); }
	reference operator[](size_type n) {
		return *(begin() + n);
	}
	reference front() { return *begin(); }
	reference back() { return *(end() - 1); }
	void push_back(const T& x) {
		if (finish != end_of_storage) {
			construct(finish, x);
			++finish;
		}
		else
			insert_aux(end(), x);
	}
};
```

## deque
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200424213635414.png)  
The data is stored by chunks of fixed size vector, which are pointered by a `map`.

```cpp
template<class T, class Ref, class Ptr, size_t BufSiz>
struct __deque_iterator {
	typedef random_access_iterator_tag iterator_category;
	typedef T value_type;
	typedef Ptr pointer;
	typedef Ref reference;
	typedef size_t size_type;
	typedef ptrdiff_t difference_type;
	typedef T** map_pointer;
	typedef __deque_iterator self;

	T* cur;
	T* first;
	T* last;
	map_pointer node;
	...
};

template<class T, class Alloc, size_t BufSiz>
typename deque<T, Alloc, BufSize>::iterator
deque<T, Alloc, BufSize>::insert_aux(iterator pos, const value_type& x) {
	difference_type index = pos - start;
	value_type x_copy = x;
	if (index < size() / 2) {
		push_front(front());
		...
		copy(front2, pos1, front1);
	}
	else {
		push_back(back());
		...
		copy_backward(pos, back2, back1);
	}
	*pos = x_copy;
	return pos;
}

inline size_t __deque_buf_size(size_t n, size_t sz) {
	// BufSiz == 0表示使用默认值
	return n != 0 ? n : (sz < 512 ? size_t(512 / sz) : size_t(1));
}

template<class T, class Alloc = alloc, size_t BufSiz = 0>
class deque {
public:
	typedef T value_type;
	// BufSiz指每个buffer大小
	typedef __deque_iterator<T, T&, T*, BufSiz> iterator;
protected:
	typedef pointer* map_pointer; // T**
protected:
	iterator start;
	iterator finish;
	map_pointer map;
	size_type map_size;
public:
	iterator begin() { return start; }
	iterator end() { return finish; }
	reference operator[](size_type n) {
		return start[difference_type(n)];
	}
	reference front() { return *start; }
	reference back() {
		iterator tmp = finish;
		--tmp;
		return *tmp;
	}
	reference operator*() const { return *cur; }
	pointer operator->() const { return &(operator*()); }
	difference_type operator-(const self& x) const {
		return difference_type(buffer_size()) * (node - x.node - 1) + (cur - first) + (x.last - x.cur);
	}

	size_type size() const { return finish - start; }
	bool empty() const { return finish == start; }

	iterator insert(iterator position, const value_type& x) {
		if (position.cur == start.cur) {
			push_front(x);
			return start;
		}
		else if (position.cur == finish.cur) {
			push_back(x);
			iterator tmp = finish;
			--tmp;
			return tmp;
		}
		else {
			return insert_aux(position, x);
		}
	}
};
```
