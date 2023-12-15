---
title: CompressIt
url: compress-it
date: 2020-02-13 15:20:00
description: 自制压缩软件
categories: Computer Science
tags: [Projects]
---

## 结构
压缩软件的核心在于压缩算法。基于Huffman编码的压缩算法思路：

 1. 以**二进制方式**读取源文件，按照每8bits作为一个字符；
 2. 统计每个字符的出现频率即为叶子结点的权值，按照Huffman算法得到每个叶子的编码；
 3. 对源文件的每个字符，将新的编码组合为二进制流，按照每8bits一个单位写入压缩文件。

举例来看：  
假设我们有待压缩源文件`hello`，`h`的ASCII码为`01101000`，同理可得整个文件的二进制形式`0110100001100101011011000110110001101111`，共5B，40bits。  
根据Huffman算法：得到`h`的编码为`00`，同理可得整个文件的Huffman编码为`0001111110`，末尾不够8bits，采用补0的方法可得`0001111110000000`，按照每8bits一个单位，写入压缩文件的是`31`和`255`对应的字符，共2B，16bits。  
解压缩流程是压缩的逆过程：

 1. 以**二进制方式**读取压缩文件；
 2. 每次取1bit，从Huffman树的根结点出发，找到某个叶子即为源字符。

## 效果
做一个简单的比较：

|压缩软件|测试文件|压缩率|测试文件|压缩率|
|--|--|--|--|--|
|CompressIt|txt(840B)|70.4%|png(282KB)|101%|
|WinRaR|txt(840B)|14.4%|png(282KB)|100%|

压缩率和压缩时间和专业软件没法比。之所以出现压缩文件大于源文件，是因为压缩文件中还存储了Huffman树等信息，为解压所需。  
对于不同内容的文件，得到的压缩文件大小也不尽相同，这主要与Huffman编码的性质有关。

## 理论分析
Huffman编码依赖于信源的统计特征，其背后的原理在于为出现频率高的字符分配尽可能短的码长，这样就可以降低平均码长：
$$L=\Sigma p_il_i$$
使得$L$最短的编码就是最优编码，可以证明Huffman编码是一种最优编码。  
同时Huffman编码还是前缀码，简化了解码过程。  
假设一种理想情况：源文件长$len$很大，共有$m$种不同字符，每个字符用8bits表示，并且每种字符出现频率$\frac{len}{m}$相同，忽略掉存储Huffman树等信息所需的空间。  
这棵完全二叉树共有结点$n=2*m-1$个，那么树深度为$h=1+\lfloor log_2n \rfloor$，每个字符的压缩长度为$h-1=\lfloor log_2n \rfloor$，故压缩后的串长度为$\frac{(h-1)*len}{8}$，可得压缩率$\frac{h-1}{8}$，即：
$$\alpha=\frac{\lfloor log_2(2*m-1) \rfloor}{8}$$
源文件中不同字符种类$m$越小，即源文件分布越集中，压缩效果越好。  
如果和定长编码比较，可以得到压缩率：
$$\alpha=\frac{\lfloor log_2(2*m-1) \rfloor}{\lceil log_2(m) \rceil}$$
$m$取值256时，Huffman树是一棵满二叉树，压缩率为100%，并不比8位固定长度编码更高效。

## 收获

 - `EOF`和`feof()`
`EOF`是一个定义在`cstdio`头文件中的宏，一般为-1：

```cpp
#define EOF (-1)
```
但是如果按照二进制读取文件，对于文件中的-1又该如何处理？  
阮一峰的博客说：

> 在Linux系统之中，EOF根本不是一个字符，而是当系统读取到文件结尾，所返回的一个信号值（也就是-1）。至于系统怎么知道文件的结尾，资料上说是通过比较文件的长度。

我们通常会写出下面程序来读取文件：

```cpp
int ch;
while ((ch = fgetc(fp)) != EOF) {
	// your code here
}
```
但是`fgetc()`在到达文件结尾和发生读取错误的情况下都会返回`EOF`，所以上述代码不严谨，采用`feof()`函数来判断文件结尾：

```cpp
int ch;
while (!feof(fp)) {
	ch = fgetc(fp);
	// your code here
}
```
但是采用`feof()`也有一个问题：读取最后一个字符后，`feof()`仍然返回0，进入循环，`fgetc()`再向后读取一个字符，`feof()`才返回1，这样程序会多循环一次。  
所以比较安全的写法是：

```cpp
int ch = fgetc(fp);
while (ch != EOF) {
	// your code here
	ch = fgetc(fp);
}
if (feof(fp))
	puts("End-of-File reached.");
else
	puts("Something went wrong.");
```

 - 虚析构函数  
基类的析构函数一般写成虚函数，做个测试：

```cpp
class base {
public:
	base() {};
	virtual ~base() {
		cout << "destructor in base" << endl;
	};

	virtual void f() {
		cout << "f in base" << endl;
	}
};

class derive :public base {
public:
	derive() {};
	~derive() {
		cout << "destructor in derive" << endl;
	};

	void f() {
		cout << "f in derive" << endl;
	}
};

base* p = new derive;
p->f();
delete p;
```
输出：

```cpp
f in derive
destructor in derive
destructor in base
```
如果基类的析构函数不是虚函数，输出：

```cpp
f in derive
destructor in base
```
结果并没有调用派生类的析构函数，造成内存泄漏。  
所以基类的虚析构函数的作用是：**当一个基类指针删除一个派生类对象，确保调用派生类的析构函数**。
 - 二进制文件  
在压缩过程中，对于不同格式源文件的读取都是采用二进制方式`rb`。  
实际上二进制文件和文本文件并没有本质区别，你所看到的内容取决于打开文件的软件对二进制流的解释方式，文件扩展名帮助计算机知道应该用哪种解释方式，通常的文本文件的解释方式有ASCII码和Unicode码。