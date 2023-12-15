---
title: C++多线程编程
url: cpp-multithreading
date: 2019-03-04 20:37:32
description: shared data is the mother of all evils
categories: Computer Science
tags: [Language,Interview]
---

## Threads & Tasks
多线程是指在一个进程中同时有多个线程运行, 这些线程间可能是独立的, 也可能进行通信. C++多线程有2种基本方式:

 - Threads: 用`std::thread`创建, 没有返回值
 - Tasks: 用`std::async`创建, 有返回值

无论哪种方式, 都可以使用以下3种方法传参:

 - pointer to function
 - functors
 - lambda function

例如有一个求和函数`sum(int& s, int l, int r)`, 单线程可能耗时很久, 因此可以用多个线程同时计算多个部分和, 最终累加部分和即可.

```cpp
// create and start each thread
std::thread t1(sum, std::ref(partialSum[0]), 0, 1000/2);
std::thread t2(sum, std::ref(partialSum[1]), 1000/2, 1000);

// main() gets blocked
t1.join();  // wait for threads to end
t2.join();
```

如果需要很多线程, 可以用数组:
```cpp
for (int i = 0; i < 10; ++i) {
	threads.push_back(thread(sum, std::ref(partialSum[i]), i * step, (i + 1) * step));
}
for (thread &t : threads) {
    if (t.joinable()) {
        t.join();
    }
}
```

除了函数指针, 还可以用Functor Object. 仿函数是实现了`operator()`的`class/struct`:

```cpp
struct cmp {
    bool operator() (const int& a, const int& b) const {
        return a < b;
    }
};
```

通过仿函数传参:
```cpp
class sumFunctor {
public:
    void operator() (int l, int r) {
        _sum = 0;
        for (int i = l; i < r; ++i) {
            _sum += i;
        }
    }
    int _sum;
};

for (int i = 0; i < 10; ++i) {
    sumFunctor* functor = new sumFunctor();
	threads.push_back(thread(std::ref(*functor), i * step, (i + 1) * step));
}
```

`thread()`构造函数的第一个参数可以有2种选择:

 - functor object: 后续无需使用仿函数的成员变量
 - ref(functor obj): 后续需要使用成员变量

除了用引用搞定返回值, 还可以将其存储到成员变量中供后续使用.

第3种方式是用lambda function传参:
```cpp
for (int i = 0; i < 10; ++i) {
    threads.push_back(thread([i, &partialSum, step] {
        for (int j = i * step; j < (i + 1) * step; ++j) {
            partialSum[i] += j;
        }
    }));
}
```

如果`sum`确实需要返回值`T`, 可以使用`std::async`生成返回值`future<T>`:
```cpp
vector<future<int>> tasks;
for (int i = 0; i < 10; ++i) {
    // create, start and push each task
    tasks.push_back(std::async(sum, i * step, (i + 1) * step));
}
// if future value not ready, main() blocks. similar to join()
for (auto& t : tasks) {
    total += t.get();  // wait for tasks to end and read return values
}
```

## Mutex & Conditional Variables

由于多个线程可能需要同时访问共享变量, 因此会产生race condition, 对最终结果造成不确定性. 最经典的例子便是计数器:
```cpp
int cnt = 0;
void increase() {
    for (int i = 0; i < 100; ++i) {
        ++cnt;
    }
}

for (int i = 0; i < 100; ++i) {
    threads.push_back(thread(increase));
}
```
100个线程, 每个线程使得`cnt`增加100, 但是最终结果却未必是10000. 核心原因在于`++cnt`不是原子操作, 可以粗略拆为3个阶段: 读取, 加一, 写入. 因此2个线程的执行顺序可以是:
```
t1: read(0)         inc  write(1)
t2:        read(0)  inc         write(1)
```
更麻烦的是, read/write也不是原子操作:
```
t1:    read1            read2 inc write
t2: read     inc  write
```
另外, 从源码开始, 编译器首先操作一波(reordering/loop unrolling), 接着CPU操作一波(out of order execution/branch prediction), 接着是多级缓存(prefetch/buffering), 最后才访问内存. 因此某个线程自认为的写入也许只是写到了自己的cache, 其他线程并未感知.

可以发现: 核心在于多个线程需要竞争访问共享资源, 并且线程间执行顺序不合法, 导致undefined behavior. 解决方案有3种:

 - mutex and locks: mutex lock/unlock, lock_guard, unique_lock, shared_lock, scoped_lock
 - `std::atomic`: memory models
 - abstraction: CSP, Actors, Map-Reduce

所谓mutex(mutual exclusion), 本质上还是为了在同一时间只有单个线程访问共享资源, 使得共享资源的访问原子化.

所以计数器程序可以更正为:
```cpp
std::mutex mtx;  // mutex is shared by all threads
int cnt;  // shared memory
void increase() {
    for (int i = 0; i < 100; ++i) {
        mtx.lock();
        ++cnt;  // critical section
        mtx.unlock();
    }
}
```
上述代码虽然是正确的, 但是可能出现意外:

 - 忘记`unlock`, 其他线程永远无法进入临界区
 - 如果临界区抛出异常, 无法调用`unlock`

需要注意的是: `lock()`两次或`unlock()`两次都是未定义行为, 应当避免.

为了减少意外, C++提供了`lock_guard`可以自动`lock/unlock`:
```cpp
std::mutex mtx;  // mutex is shared by all threads
int cnt;  // shared memory
void increase() {
    for (int i = 0; i < 100; ++i) {
        lock_guard<mutex> guard(mtx);
        ++cnt;
    }
}
```
构造时自动加锁, 析构时(如跳出作用域)自动解锁, 这样上述2个问题就可以避免, 这就是所谓的RAII.

`lock_guard`没有提供`lock/unlock`接口, 不够灵活. 因此C++引入了`unique_lock`: `lock_guard` + `lock/unlock`接口.
```cpp
std::mutex mtx;  // mutex is shared by all threads
int cnt;  // shared memory
void increase() {
    for (int i = 0; i < 100; ++i) {
        unique_lock<mutex> ul(mtx);
        ++cnt;
        ul.unlock();
        ul.lock();
    }
}
```

为了提高并发效率, 可以让多个线程同时读取, 于是就有了`shared_lock`:
```cpp
std::shared_mutex mtx;
int cnt;  // shared memory
void increase() {
    for (int i = 0; i < 100; ++i) {
        unique_lock<shared_mutex> ul(mtx);  // unique_lock for writers
        ++cnt;
    }
}
void reader() {
    for (int i = 0; i < 100; ++i) {
        shared_lock<shared_mutex> ul(mtx);  // shared_lock for readers
        cout << cnt;
    }
}
```

如果需要多个互斥量, 那么要注意避免死锁:
```cpp
std::mutex mtx1, mtx2;
int cnt;
void increase1() {
    for (int i = 0; i < 100; ++i) {
        lock_guard<mutex> lock1(mtx1);
        lock_guard<mutex> lock2(mtx2);
        ++cnt;
    }
}
void increase2() {
    for (int i = 0; i < 100; ++i) {
        lock_guard<mutex> lock2(mtx2);
        lock_guard<mutex> lock1(mtx1);
        ++cnt;
    }
}
```
正确的处理是`lock()`, 通过all or nothing避免死锁:
```cpp
void increase() {
    for (int i = 0; i < 100; ++i) {
        lock(mtx1, mtx2);  // lock both mutexes without deadlock
        // make sure the locked mutexes unlocked at the end of scope
        lock_guard<mutex> lock1(mtx1, std::adopt_lock);
        lock_guard<mutex> lock2(mtx2, std::adopt_lock);
        ++cnt;
    }
}
```
上述代码虽然正确, 但很凌乱, 因此C++17提供了`scoped_lock`, 仍然通过RAII的方式避免手动解锁:
```cpp
void increase() {
    for (int i = 0; i < 100; ++i) {
        scoped_lock lck(mtx1, mtx2);
        ++cnt;
    }
}
```

ok, 到此为止解决了互斥访问的问题, 接下来解决线程间通信的问题, 由此引入条件变量. 线程间通信最经典的例子就是生产者-消费者问题, 当准备好数据后, 生产者需要某种方式通知消费者:

 - 共享内存: 设置共享变量`data`和`ready`. 生产者准备好数据后, 将`ready=true`; 消费者忙等监测`ready`的状态.
 - 条件变量: 为了提高效率, 生产者准备好数据后通过条件变量发送通知; 消费者接到通知后才唤醒. It is all about sending a message.

```cpp
mutex mtx;
condition_variable cv;
bool ready = false;
data = 0;
void producer() {
    while (true) {
        unique_lock<mutex> ul(mtx);
        data = generateData();
        ready = true;
        ul.unlock();
        cv.notify_one();
        ul.lock();
        cv.wait(ul, [](){ return ready == false; });
    }
}

void consumer() {
    while (true) {
        unique_lock<mutex> ul(mtx);
        // if blocked, ul.unlock() is called automatically
        // if unblocked, ul.lock() is called automatically
        cv.wait(ul, []() { return ready; });
        sample(data);
        ready = false;
        ul.unlock();
        cv.notify_one();
        ul.lock();
    }
}
```
注意到`wait`是需要2件事才能唤醒的:

 - 收到`cv`的通知: `cv.notify_one()`
 - `ready == true`

之所以仍然需要`ready`变量, 是因为`wait`可能出现虚假唤醒的情况.

## 生产者消费者
本质上是通过引入缓冲区来平衡生产速度和消费速度，包括单生产者-单消费者，单生产者-多消费者，多生产者-单消费者和多生产者-多消费者。

[实现在这里]()

有几个需要注意的地方：
```cpp
while (q.empty()) {
    cv.wait(lock);
}
```
使用`while`而不是`if`，因为`wait`从阻塞到返回并不一定是由`notify_one()`造成的，还可能由于其他原因导致，即伪唤醒，这样就会导致后续执行出错。

由于单独使用`mutex`可能导致死锁，因此选择使用`unique_lock`管理互斥锁。之所以不能用`lock_guard`，因为`lock_guard`只能是在构造时自动调用`lock()`上锁，析构时自动释放锁，即所谓的RAII(资源分配即初始化)，没有`lock()`和`unlock()`接口供程序员使用。

## 多线程顺序打印
```cpp
#include <iostream>
#include <deque>
#include <thread>
#include <mutex>
#include <condition_variable>
using namespace std;

mutex mtx;
condition_variable cv;
int flag = 0;

void print(int num) {
    for (int i = 0; i < 10; ++i) {
        std::unique_lock<std::mutex> lk(mtx);
        cv.wait(lk, [&]() { return num == flag; });
        std::cout << char('A' + num);
        flag = (flag + 1) % 3;
        cv.notify_all();
    }
}

int main() {
    std::thread t1(print, 0);
    std::thread t2(print, 1);
    std::thread t3(print, 2);
    
    t1.join();
    t2.join();
    t3.join();

    std::cout << "\n" << "main thread";
    return 0;
}
```

## 线程安全的队列

STL的`std::queue<T>`并不是线程安全的，举个栗子：

```cpp
if (!queue.empty()) {
    // 线程T1执行至此，T2此时将唯一的元素pop，T1继续
    queue.front();
}
```

```cpp
#include <bits/stdc++.h>

template<typename T>
class Queue {
public:
    Queue() = default;
    Queue(const Queue<T> &) = delete;
    Queue<T>& operator=(const Queue<T> &) = delete;

    Queue(Queue<T>&& other) {
        queue_ = std::move(other.queue_);
    }

    virtual ~Queue() {}

    bool empty() const {
        return queue_.empty();
    }
    
    std::optional<T> pop() {
        if (queue_.empty()) {
            return {};
        }
        T tmp = queue_.front();
        queue_.pop();
        return tmp;
    }

    void push(const T& item) {
        queue_.push(item);
    }

private:
    std::queue<T> queue_;
};

template<typename T>
class SharedQueue {
public:
    SharedQueue() = default;
    SharedQueue(const SharedQueue<T> &) = delete;
    SharedQueue<T>& operator= (const SharedQueue<T> &) = delete;

    SharedQueue(SharedQueue<T>&& other) {
        std::lock_guard<std::mutex> lck(other.mtx);
        queue_ = std::move(other.queue_);
    }

    virtual ~SharedQueue() {}

    bool empty() const {
        std::lock_guard<std::mutex> lck(mtx);
        return queue_.empty();
    }

    void push(const T& item) {
        std::lock_guard<std::mutex> lck(mtx);
        queue_.push(item);
        cv.notify_one();
    }

    std::optional<T> pop() {
        std::lock_guard<std::mutex> lck(mtx);
        if (queue_.empty()) {
            return {};
        }
        T tmp = queue_.front();
        queue_.pop();
        return tmp;
    }

    std::optional<T> popBlock() {
        std::unique_lock<std::mutex> lck(mtx);
        cv.wait(lck, [&] { return !queue_.empty(); });
        T tmp = queue_.front();
        queue_.pop();
        return tmp;
    }

private:
    std::queue<T> queue_;
    mutable std::mutex mtx;
    std::condition_variable cv;
};

Queue<int> q;
SharedQueue<int> sq;

void producer() {
    for (int i = 0; i < 100; ++i) {
        sq.push(i);
        std::this_thread::sleep_for(std::chrono::seconds(3));
    }
}

void consumer1() {
    while (1) {
        std::printf("[1]  -------   %d\n", *sq.popBlock());
    }
}

void consumer2() {
    while (1) {
        auto front = sq.pop();
        std::printf("[2]  -------   %d\n", front ? *front : -1);
        std::this_thread::sleep_for(std::chrono::seconds(1));
    }
}

int main() {
    std::thread t1(producer);
    std::thread t2(consumer2);
    
    t1.join();
    t2.join();
    return 0;
}
```

## Ref
[The Producer Consumer Problem in C++](https://andrew128.github.io/ProducerConsumer/)  
[producer-consumer](https://gist.github.com/ictlyh/f8473ad0cb1008c6b32c41f3dea98ef5)  
[生产者消费者模型](https://immortalqx.github.io/2022/02/08/cpp-notes-6/)  
[C++ multithread](https://www.youtube.com/watch?v=3aqxaZsvn80)  
[使用C++手写线程安全队列](https://zhuanlan.zhihu.com/p/355113813)