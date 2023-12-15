---
title: C/C++ FAQ
url: cpp-faq
date: 2019-03-04 20:37:32
description: C/C++常见问题
categories: Computer Science
tags: [Language,Interview]
---

## 指针和引用

1. 指针是一个新的变量，存储另一个变量的地址，可以通过指针修改另一个变量；引用是一个别名，对引用的操作就是对变量本身的操作。
2. 指针可以有多级；引用只有一级。
3. 指针的大小一般4B；引用的大小一般取决于被引用对象大小。
4. 指针可以为空；引用不能为空。
5. 传参选择：传参时传引用与传指针效果相同，传引用，没有产生实参的副本，直接对实参操作，传指针，被调函数需要给形参分配空间，可读性差，需要传地址做实参，传引用更简单清晰。返回被调函数局部变量的内存时传指针，使用后及时释放避免内存泄漏；返回局部变量的引用没有意义，会自动销毁。传指针需要单独开辟内存；在对栈空间大小敏感时（如递归）传引用，无需创建临时变量，开销更小。类对象作为参数时传引用是C++传递类对象的标准方式。

引用只是一个别名，不是一种数据类型，不占存储空间，不能建立数组的引用  
引用必须初始化，指针不必  
引用初始化后不能改变，指针可以改变指向的对象  
不存在指向空值的引用，存在指向空值的指针

## 空类
```cpp
class A {

};
// sizeof(A) = 1
```
空类的大小之所以为1，因为标准规定完整对象的大小>0，否则两个不同对象可能拥有相同的地址，故编译器会生成1B占位符。
那么两个对象为什么不能地址相同呢？

> There would be no way to distinguish between these two objects when referencing them with pointers.

空类中到底都有什么呢？

```cpp
class A {
public:
	A();  // 默认构造函数
	A(const A&);  // 拷贝构造函数
	~A();  // 析构函数
	A& operator=(const A&);  // 赋值运算符
	A* operator&();  // 取址运算符（非const）
	const A* operator&() const;  // 取址运算符（const）
};
```
仅仅声明一个类，不会创建这些函数。只有当定义类的对象时，才会产生。

## 多态Polymorphism
OOP的特点包括封装、继承和多态。

封装的目的是将类的**实现**和**使用**分离，隐藏实现细节，保留部分接口和方法供外人使用，避免程序互相依赖，使得模块松耦合。  
继承是为了复用基类中的属性和方法，实现了代码的可重用性。  
多态按照字面意思：同一接口的多种不同的实现方式，同种操作作用于不同的对象可以产生不同的行为和执行结果。可以有效**避免代码冗余重复，增加程序的可扩展性**，重复是万恶之源！！

根据状态确定时间分为静态多态（模板多态）和动态多态，静态多态在编译时就确定了，支持某些公共的函数，类之间的定义是独立的；动态多态运行时确定。

静态多态主要包括函数重载和运算符重载，动态多态主要通过继承和虚函数实现。定义虚函数`f`，是为了用基类的引用或指针调用派生类的`f`，最终调用哪个`f`取决于对象的实际类型（基类还是派生类），即在运行时选择函数的版本，也就是所谓的**动态绑定**。

```cpp
class Animal {
public:
  Animal() {}
  ~Animal() {}

  void f() {
    cout << "animal" << endl;
  }
};

class Dog : public Animal {
public:
  Dog() {}
  ~Dog() {}

  void f() {
    cout << "dog" << endl;
  }
};

Animal* animal = new Dog;
animal->f();  // 输出animal  
```
将函数`f`改为虚函数：
```cpp
class Animal {
public:
  Animal() {}
  virtual ~Animal() {}

  virtual void f() {
    cout << "animal" << endl;
  }
};

class Dog : public Animal {
public:
  Dog() {}
  virtual ~Dog() {}

  virtual void f() {
    cout << "dog" << endl;
  }
};

Animal* animal = new Dog;
animal->f();  // 输出dog 
```

当基类指针指向派生类对象，用该指针调用同名成员函数时，基类声明为虚函数（子类可以不写`virtual`）就会调用派生类的`f`，否则调用基类的`f`。

如果没有虚函数，管理大量的派生类对象`Dog/Cat`等就需要声明相应的指针，有虚函数的话，就可以声明为指针数组`Animal* animals[5]`指向相应的派生类对象，然后直接`animals[i]->f()`即可。

### 构造函数和析构函数
析构函数可以并且应当是虚函数，因为要确保执行相应对象的析构函数。如果基类指针指向派生类对象，会调用派生类的析构函数并释放资源，然后调用基类的析构函数。当析构一个指向子类的父类指针时，编译器可以根据虚函数表寻找到子类的析构函数进行调用，从而正确释放子类对象的资源。否则`delete animal`时调用`Animal`的析构函数，`Dog`的资源未释放。如果析构函数不被声明成虚函数，则编译器实施静态绑定，在删除指向子类的父类指针时，只会调用父类的析构函数而不调用子类析构函数，这样就会造成子类对象析构不完全造成内存泄漏。

```cpp
/**
 * 构造时，先调用父类构造函数，再调用子类构造函数
 * 析构时，先调用子类析构函数，再调用父类析构函数
 * 
 * 子类构造函数流程：
 *  1. 调用父类构造函数
 *  2. 把CDerived的虚函数表地址赋值给对象的虚函数表指针
 *  3. 初始化CDerived的成员变量
 *  4. 调用函数Init()
 * 
 * 父类构造函数流程：
 *  1. 把CBase的虚函数表地址赋值给对象的虚函数表指针
 *  2. 初始化CBase的成员变量
 *  3. 调用函数Init()
 * 
 * 子类析构函数流程：
 *  1. 把CDerived的虚函数表地址赋值给对象的虚函数表指针
 *  2. 调用函数Uninit()
 *  3. 调用父类CBase的析构函数
 * 
 * 父类析构函数流程：
 *  1. 把CBase的虚函数表地址赋值给对象的虚函数表指针
 *  2. 调用函数Uninit()
 * 
 * 构造函数或析构函数中调用虚函数与普通函数调用方式一样，相当于调用CBase::Init()
 * 由于编译器对构造函数和析构函数做了特殊处理，因此不会多态
 * 
 * 虚函数调用过程：
 *  1. 获取this指针的地址
 *  2. 通过this指针得到虚函数表地址，通常this指针指向vptr
 *  3. 虚函数表地址加上函数在表内偏移量得到函数地址
 *  4. 通过call命令调用函数
 * 
 * 调用父类还是子类的函数，关键在于vptr指向父类还是子类的虚函数表
 * 构造函数和析构函数中，vptr均设置为指向当前类的虚函数表，因此均调用当前类的函数
 */

class Base {
public:
	virtual void f() {
		cout << "Base";
	}
	virtual void g() {}
private:
	int i;
};

class Derived : public Base {
public:
	virtual void f() {  // 重写f
		cout << "Derived";
	}
	virtual void h() {}
private:
	int j;
};

int main() {
	Base* p = new Derived();
	p->f();  // 调用派生类的f()
	delete p;
	return 0;
}
```
基类指针`p`调用虚函数`f`，`f`作用的可能是基类对象，也可能是派生类对象，这就是多态（同样消息作用于不同类型对象产生不同的行为）的一种方式，即动态多态。
正因为编译器无法确定使用哪个虚函数，所以所有的**虚函数必须定义**，否则编译器会报错。

**构造函数不能是虚函数**：

1）因为创建一个对象时需要确定对象的类型，而虚函数是在运行时确定其类型的。在构造一个对象时，**由于对象还未创建成功，编译器无法知道对象的实际类型**，是类本身还是类的派生类等。

2）虚函数的调用需要vptr，而该指针存放在对象的内存空间中；若构造函数声明为虚函数，那么由于对象还未创建，还没有内存空间，vptr还未初始化，无法调用构造函数了。

C++他爹Bjarne Stroustrup是这么说的：

> A virtual call is a mechanism to get work done given partial information. In particular, "virtual" allows us to call a function knowing only an interfaces and not the exact type of the object. To create an object you need complete information. In particular, you need to know the exact type of what you want to create. Consequently, a "call to a constructor" cannot be virtual.

构造函数或者析构函数中调用虚函数会怎样？  
在构造函数中调用虚函数，由于当前对象还没有构造完成，此时调用的虚函数指向的是基类的函数实现方式，无法[实现多态](https://www.zhihu.com/question/35632207)。  
在析构函数中调用虚函数，此时调用的是子类的函数实现方式。

函数只声明不定义有什么问题？  
类中普通函数没问题，虚函数链接时会报错`undefined reference to vtable for Base`，无法获取虚函数的地址并填充虚表。[参考](https://python.iitter.com/other/173960.html)

[静态函数不能为虚函数](https://www.geeksforgeeks.org/can-static-functions-be-virtual-in-cpp/)

空指针调用普通的成员函数，如果函数体用到了this指针（访问了非静态变量），程序崩溃，否则可以正常调用
不能调用虚函数（运行时报错），

### 虚函数表 & 虚函数指针
编译器处理虚函数的方法是：如果类中有虚函数，就将虚函数的地址记录在类的虚函数表中。派生类在继承基类的时候，如果有重写基类的虚函数，就将虚函数表中相应的函数指针设置为派生类的函数地址，否则指向基类的函数地址。为每个类的实例添加一个虚表指针（vptr），虚表指针指向类的虚函数表。实例在调用虚函数的时候，通过vptr找到类中的虚函数表，找到相应的函数进行调用。

一个类的所有实例共享同一张虚表：

![虚函数表与虚函数指针](虚表.png)

### 纯虚函数
与虚函数必须定义相反，纯虚函数无须定义（要定义必须在类的外部），含有纯虚函数的类是**抽象基类**。
抽象基类定义好接口，继承该类的其他类可以覆盖这个接口。

```cpp
virtual void f() = 0;  // 声明纯虚函数
```
之所以要引入纯虚函数，是因为很多时候基类产生对象是没有意义的。比如动物类可以派生出狗、猪等子类，但动物类生成对象毫无意义。
因此，不能创建抽象基类的对象，派生类必须覆盖(override)以定义自己的`f`，否则派生类仍然是抽象基类。

用基类的指针（引用）调用虚函数时就发生动态绑定：运行时，虚函数根据绑定对象的实际类型，选择调用函数的版本。
[虚函数sizeof](https://www.nowcoder.com/questionTerminal/8262288d505d4fc599ebd9c8e7fd86ce)

## 重载 & 覆盖 & 重写

 - 重载(overload)：在类内部发生。函数名相同，参数个数、参数类型、参数顺序至少有一种不同。返回值类型可以相同，也可不同；
 - 覆盖(override)：覆盖基类的虚函数。函数名相同，参数相同，基类函数必须是虚函数；

```cpp
struct B {
	virtual void f1(int) const;
	virtual void f2();
	void f3();
};

struct D1 :B {
	void f1(int) const override;  // 正确：f1与基类中的f1匹配
	void f2(int) override;  // 错误：B没有形如f2(int)的函数
	void f3() override;  // 错误：f3不是虚函数
	void f4() override;  // 错误：B没有名为f4的函数
};
```
 - 重写(overwrite)：派生类的函数屏蔽了同名的基类函数：
派生类函数与基类函数同名，参数不同。不论基类函数是否为虚函数，都会被隐藏；
派生类函数与基类函数同名，参数相同。基类函数不为虚函数，会被隐藏；

[函数重载按照返回类型](https://www.zhihu.com/question/21455159)  
[C++函数重载](https://www.cnblogs.com/skynet/archive/2010/09/05/1818636.html)

## 关键字
### static
C++中`static`关键字用来**声明类的成员**：

 - 类的静态成员变量或函数属于类而非对象，只有一份副本，搞成虚函数也没有意义；
 - 静态成员函数没有`this`指针，只能访问类的静态数据，无法访问类的成员vptr，所以静态成员函数不能定义为虚函数；
 - 静态成员变量初始化`int Base::name = 0`

如果不是在类中声明成员，还有下面用法：

 - 隐藏作用：多文件编译时，定义的全局变量和函数都是整个工程可见的，只要使用时加上`extern`关键字即可。如果加上`static`关键字，那么该变量或函数就变为**仅当前文件**可见，这样我们可以在不同文件中定义同名的变量或函数而不用担心冲突。
 - 全局生存期：`static`变量存储在静态数据区，默认值为0，**只被初始化一次**，即使作为局部变量，生存期也为整个程序，但作用域与普通变量相同，退出函数后即使变量存在，但不能使用。

在C语言中主要有[2种用途](https://stackoverflow.com/questions/572547/what-does-static-mean-in-c):

 - 函数内的`static`变量在函数结束后仍然保持其值;
 - `static`全局变量/函数只能在其定义的文件中调用, 可以用来进行访问控制和封装.

### [const](https://zhuanlan.zhihu.com/p/110159656)

 - 定义const对象：一旦创建其值不能改变，故const对象必须初始化。

```cpp
const int bufSize = 512;
int const bufSize = 512;  // the same as the previous one
```
由于const对象默认只在文件内有效，所以如果要在文件间共享：

```cpp
// file1.cpp定义并初始化
extern const int bufSize = 512;
// file1.h可以仅声明，不初始化
extern const int bufSize;
```
 - 常量指针（const pointer）：指针本身（存在指针中的地址）不可变，不能指向其他变量

```cpp
int num = 0;
int* const p = &num;  // p将一直指向num
```
 - 指向常量的指针（pointer to const）：无法通过指针修改对象。

```cpp
double pi = 3.14;
const double* p = &pi;  // 正确
*p = 4.1;  // 错误，不能通过p修改pi的值 
```
 - 修饰成员函数

```cpp
class A {
	void f() const;  // 不能修改成员变量，const对象不能调用非const成员函数
};
```
 - 修饰类对象

```cpp
class A {
	void f1();
	void f2() const;
};

const A obj;  // obj为常量对象，任何成员都不能被修改，任何非const成员函数都不能被调用
obj.f1();  // 错误
obj.f2();  // 正确

const A* obj  = new A();
obj->f1();  // 错误
obj->f2();  // 正确
```
 - 转为非const

```cpp
const char* pc;  // pc指向内容不可变
char* p = const_cast<char*>(pc);  // 正确，但是通过p写值是未定义行为
```

```cpp
// a和b必须是独立内存区域
void swap(int& a, int& b) {
	a = a ^ b;
	b = a ^ b;
	a = a ^ b;
}
```

### inline
优点是没有函数调用开销，加快运行；缺点是增大代码体积，容易出现page fault从而拖慢性能。  
[内联函数](https://algorithmlixuan.github.io/2018/10/11/c++lession4/)

### new/delete/malloc/free

 - `new/delete`是C++运算符，需要编译器支持，所以不需要指定大小，返回相应对象类型的指针。`malloc/free`是库函数，不由编译器控制，需要显式指出大小，返回`void*`，需要强制类型转换。
 - `new`会调用`operator new()`申请内存(用`malloc`实现)，调用构造函数初始化成员变量，返回相应指针，`delete`先调用析构函数，再调用`operator delete()`函数释放内存(用`free`实现)。
 - new分配失败会抛出`std::bad_alloc`异常。malloc分配失败返回`NULL`指针，无法完成对象的构造和析构。
 - `new/delete/malloc/free`都是线程安全的，通过锁实现。但不是可重入的。

### volatile
编译器对加`volatile`关键字的变量的代码不进行编译器优化，保证对特殊地址的稳定访问。不能把他放在cache或寄存器中重复使用，防止优化编译器把变量从内存装入CPU寄存器。两个线程有可能一个使用内存中的变量，一个使用寄存器中的变量，这会造成程序的错误执行。总是重新从内存读取数据，即使前面的指令刚刚从该内存地址读取过。
```cpp
volatile int i = 10;
int a = i;
int b = i;
```
多任务间每个任务共享的标志应该[加volatile](https://zhuanlan.zhihu.com/p/62060524)。

## 类型转换
类型转换分为隐式转换和显式转换。
显式转换有四种：

 - `static_cast`
没有底层const都可以，使用比较普遍。
基类->派生类：不安全
主要执行非多态转换，代替C中的转换。

```cpp
void* p = &d;
double* dp = static_cast<double*>(p);
```

 - `dynamic_cast`
运行时类型检查，
将基类指针或引用安全转换为派生类的指针或引用：

```cpp
// type是类，且有虚函数
dynamic_cast<type*>(e);  //e是指针
dynamic_cast<type&>(e);  //e是左值
dynamic_cast<type&&>(e);  //e不是左值
```

 - `const_cast`
改变底层const。
常量指针转为非常量指针。

```cpp
const char* cp;
char* q = static_cast<char*>(cp);  // wrong, static_cast不能用于底层const
char* p = const_cast<char*>(cp);  // true
```

 - `reinterpret_cast`
比较危险，不太用。处理无关类型转换，重新解释对象的比特模型。

## 智能指针
[线程安全问题1](https://stackoverflow.com/questions/14482830/stdshared-ptr-thread-safety)  
[线程安全问题2](https://www.quora.com/Is-the-C-smart-pointer-a-thread-safe-design)

> All member functions (including copy constructor and copy assignment) can be called by multiple threads on different instances of shared_ptr without additional synchronization even if these instances are copies and share ownership of the same object. If multiple threads of execution access the same instance of shared_ptr without synchronization and any of those accesses uses a non-const member function of shared_ptr then a data race will occur; the shared_ptr overloads of atomic functions can be used to prevent the data race.

避免内存泄漏或二次释放，引入智能指针：

 - `shared_ptr`
允许多个指针指向同一个对象。通常与`make_shared`函数结合食用：

```cpp
shared_ptr<string> p = make_shared<string>(10, '9');
```
实现方式一般是reference counting，在堆上申请资源并返回指针后，在堆上申请一个共享的引用计数器，每来一个指针指向该对象，++计数器。当计数器为0时，会自动释放指向的对象。    
面试有可能被要求手撕一个：2个指针成员，一个指向对象，一个指向计数器
```cpp
template<class T>
class mySharePtr {
public:
	mySharePtr() :refCnt(nullptr), ptr(nullptr) {}

	mySharePtr(T* res) :refCnt(nullptr), ptr(res) {
		add();
	}

	mySharePtr(const mySharePtr<T>& p) :refCnt(p.refCnt), ptr(p.ptr) {
		add();
	}

	virtual ~mySharePtr() {
		remove();
	}

	// lvalue is assigned, --counter
	mySharePtr<T>& operator=(const mySharePtr<T>& that) {
		if (this != &that) {
			remove();
			this->ptr = that.ptr;
			this->refCnt = that.refCnt;
			add();
		}
		return *this;
	}

	bool operator==(const mySharePtr<T>& other) {
		return ptr == other.ptr;
	}

	bool operator!=(const mySharePtr<T>& other) {
		return !operator==(other);
	}

	T& operator*() const {
		return *ptr;
	}

	T* operator->() const {
		return ptr;
	}

	int numRef() const {
		if (refCnt) {
			return *refCnt;
		}
		else {
			return -1;
		}
	}
protected:
	// if null, create counter = 1, else ++counter
	void add() {
		if (refCnt) {
			++(*refCnt);
		}
		else {
			refCnt = new int(1);
		}
	}

	// --counter, if counter = 0, free memory
	void remove() {
		if (refCnt) {
			--(*refCnt);
			if (*refCnt == 0) {
				delete refCnt;
				delete ptr;
				refCnt = nullptr;
				ptr = nullptr;
			}
		}
	}
private:
	int* refCnt;
	T* ptr;
};
```

 - `unique_ptr`
看名字就知道，独占对象。离开`unique_ptr`对象的作用域时，自动释放资源，RAII。

 - `weak_ptr`
[std::weak_ptr](https://www.cnblogs.com/yocichen/p/10563124.html)不控制对象生命周期，作为观察者指向`shared_ptr`管理的对象，解决循环引用问题。

## 成员变量初始化顺序

初始化成员列表好处：

 1. const成员变量只能初始化不能赋值
 2. 引用只能在定义时初始化，不能重新赋值
 3. 高效：初始化列表比赋值操作少一次默认构造函数，因为程序要默认构造临时对象（等号右边）后才能赋值
	
## 指针函数和函数指针
```cpp
// 指针函数: 返回类型是指针的函数
int* add(int a, int b) {
    int* s = new int(a + b);
    return s;
}

int sub(int a, int b) {
    return a - b;
}

int operation(int a, int b, int (*func)(int, int)) {
    return (*func)(a, b);
}

int (*minus)(int, int) = sub;  // minus是函数指针
int* m = add(1, 2);
int n = operation(3, *m, minus);
```

`class`的成员变量和成员函数默认都是`private`，`struct`的成员变量和成员函数默认都是`public`。`class`的继承默认是私有继承，`struct`的继承默认是公有继承，公有还是私有取决于子类而非父类。`class`可以用作模板，`struct`不可以。

基类静态变量/全局变量：静态成员变量必须类外初始化
派生类静态变量/全局变量
基类成员变量：按照在类中定义的顺序，而不是初始化列表中的顺序
派生类成员变量

范围for循环
```cpp
for (int& i : nums)  // allow modification in nums
for (int i : nums)  // access by value
```

```cpp
enum class CoordinateArea { ONE, TWO };
CoordinateArea a = CoordinateArea::ONE;
```

## STL

[C++11新特性](https://www.jianshu.com/p/78c700c8d72d)：自动类型推导、范围for、Lambda表达式、智能指针、
### [迭代器失效](https://www.cnblogs.com/linuxAndMcu/p/14621819.html)
```cpp
for (auto it = vec.begin(); it != vec.end();) {
    if () {
        it = vec.erase(it);    
    } else {
        ++it;
    }
}
```

### 优先队列

[优先队列的三种方式](https://zhuanlan.zhihu.com/p/344121142)  
[自定义排序](https://www.cnblogs.com/shona/p/12163381.html)

### vector
 - [vector的push_back过程](https://zhuanlan.zhihu.com/p/377186496)
 - [std::vector::reserve](https://yasenh.github.io/post/cpp-diary-2-reserve-resize/)
 - [push_back vs emplace_back](https://yasenh.github.io/post/cpp-diary-1-emplace_back/)
 - 并发读是线程安全的，STL的所有容器并发写都[不是线程安全的](https://stackoverflow.com/questions/11144294/are-stdvectors-threadsafe)。
 - [扩容时也是线程不安全的](https://segmentfault.com/a/1190000041334904)。vector扩容时，内存位置发生改变导致Segmentation fault错误。因为vector在扩容时会将内容全部拷贝到新的内存区域中，原有的内存区域被释放，此时如果有线程依然在向旧的内存区域读或写就会出问题。
 - `clear`将size置0，不会改变capacity，不会释放内存，可以用`vector<int>().swap(vec)`来释放内存。


### map
[map与unordered_map](https://zhuanlan.zhihu.com/p/48066839)

## 全局变量
Global variables in a single translation unit (source file) are initialized in the order in which they are defined. The order of initialization of global variables in different translation units is unspecified.  
[ref1](https://stackoverflow.com/questions/3746238/c-global-initialization-order-ignores-dependencies)  
[ref2](https://gamedev.stackexchange.com/questions/91958/why-it-s-recommended-to-keep-global-variable-initialization-and-the-objects-con)

## 内存池
常规的动态内存申请需要系统调用，内核也是维护一个空闲块池，系统调用有开销。内存池在用户空间实现了内存申请释放，速度提升。

函数缺省：
	某个参数有默认值，缺省参数仍在后边
	调用时如果略去一个参数传递，则略去后面所有
	
异常处理：
	抛出异常，没有被特定的catch语句捕获，函数调用堆栈会被解退（函数终止，销毁局部变量，控制权转到调用它的那个函数），
	并在下一个外层try..catch捕获，最后没有任何catch捕获，调用terminate，abort退出。

模板特化、偏特化

数据库缓存一致：
	并发操作导致不一致，本质上修改数据库和删除缓存耦合在一起，使得其他操作有可能读出脏数据
	解决方案：解耦，延迟双删：写->删缓存->修改数据库->延时->再次删缓存
	二：内存队列：写修改数据库，将数据id放入队列，消费者线程消费即可
	
操作系统
* 用户告诉操作系统执行hello程序
* 操作系统到硬盘找到该程序
* 由编译程序将用户源程序编译成若干个目标模块
* 由链接程序将目标模块和相应的库函数链接成装入模块
* 操作系统分配内存，由装入程序将装入模块装入内存
* 为执行hello程序创建执行环境（创建新进程）
* 操作系统设置CPU上下文环境，并跳到程序开始处
* 程序的第一条指令执行
* 程序执行与printf对应的系统调用
* 操作系统分配设备
* 执行显示驱动程序
* 窗口系统将像素写入存储映像区
