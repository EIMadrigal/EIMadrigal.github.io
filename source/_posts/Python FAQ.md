---
title: Python FAQ
url: python-faq
date: 2021-01-30 11:01:00
description: Python面试
categories: Computer Science
tags: [Language,Interview]
---

## lambda表达式
对于一句话的函数，均可以用lambda表达式，一种语法糖。
```python
def f(x):
    return x ** 2

f = lambda x: x ** 2
g = lambda x, y: x + y
a.sort(key=lambda x: x[1])

def quadratic(a, b, c):
    return lambda x: a*x*x + b*x + c

f = quadratic(1, -1, 2)
f(5)

quadratic(1, -1, 2)(5)
```

## decorator
```python
# messy
def add(a, b):
    t1 = time.time()
	c = a + b
    t2 = time.time()
    print(t2 - t1)
	return c
```

如果使用装饰器：
```python
def display_time(func):
    def wrapper(*args):
        t1 = time.time()
        res = func(*args)  # do something
        t2 = time.time()
        print(t2 - t1)
        return res
    return wrapper

@display_time
def add(a, b):
	return a + b

ans = add(1, 2)
print(ans)
```

## GIL
是一个防止多线程并发执行的mutex，不是python的特性而是其解释器CPython引入的，使得在任何时刻仅有一个线程在执行，保证线程安全，但是影响多线程执行效能，无法利用多个CPU核心的优势。

## 易错
[`a, b = b, a`赋值问题](https://stackoverflow.com/questions/51950193/python-a-b-b-a-implementation-how-is-it-different-from-c-swap-function)  
`print(1 != 0 != 1)`输出`True`，因此不能用来判断3个数是否不等。

## Basics
1. 两个列表转字典：`dict(zip(list1, list2))`
2. [Python中__new__()和__init__()的区别](https://blog.csdn.net/weixin_37579123/article/details/89515577)：Python中的constructor是`__new__`，initializer是`__init__`。`__new__`首先被调用创建新实例并返回该实例，始终作为类的静态方法，类级别方法，默认调用父类的`__new__`构造该类的实例，也可以重写`__new__`，构造出来的对象取决于`__new__`，`__new__`返回什么就是什么；`__init__`没有返回值负责初始化创建的实例，实例级别方法，其`self`参数就是`__new__`返回的实例。
3. [Python中的进程/线程/协程](https://hj24.life/posts/%E8%BF%9B%E7%A8%8B%E7%BA%BF%E7%A8%8B%E5%92%8C%E5%8D%8F%E7%A8%8B%E6%B5%85%E6%9E%90/)：
协程是比线程更小的执行单元，也叫微线程，用户态，在单线程上执行多个任务，一个任务等待时执行另一个任务，使用的内存动态变化，切换调度由用户控制，避免线程的系统级别切换的开销，内存利用率更高。
Python的协程是1：N

```python
animals = ['cat', 'dog', 'monkey']
for idx, animal in enumerate(animals):
    print('#%d: %s' % (idx + 1, animal))
# Prints "#1: cat", "#2: dog", "#3: monkey", each on its own line
```

```python
d = {'person': 2, 'cat': 4, 'spider': 8}
for animal, legs in d.items():
    print('A %s has %d legs' % (animal, legs))
# Prints "A person has 2 legs", "A cat has 4 legs", "A spider has 8 legs"
```

```python
animals = {'cat', 'dog', 'fish'}
for idx, animal in enumerate(animals):
    print('#%d: %s' % (idx + 1, animal))
# Prints "#1: fish", "#2: dog", "#3: cat"
```
one of the most important differences is that tuples can be used as keys in dictionaries and as elements of sets, while lists cannot.


```python
class Greeter(object):

    # Constructor
    def __init__(self, name):
        self.name = name  # Create an instance variable

    # Instance method
    def greet(self, loud=False):
        if loud:
            print('HELLO, %s!' % self.name.upper())
        else:
            print('Hello, %s' % self.name)

g = Greeter('Fred')  # Construct an instance of the Greeter class
g.greet()            # Call an instance method; prints "Hello, Fred"
g.greet(loud=True)   # Call an instance method; prints "HELLO, FRED!"
```

```python
import numpy as np

a = np.array([1, 2, 3])   # Create a rank 1 array
print(type(a))            # Prints "<class 'numpy.ndarray'>"
print(a.shape)            # Prints "(3,)"
print(a[0], a[1], a[2])   # Prints "1 2 3"
a[0] = 5                  # Change an element of the array
print(a)                  # Prints "[5, 2, 3]"

b = np.array([[1,2,3],[4,5,6]])    # Create a rank 2 array
print(b.shape)                     # Prints "(2, 3)"
print(b[0, 0], b[0, 1], b[1, 0])   # Prints "1 2 4"
```

```python
import numpy as np

a = np.zeros((2,2))   # Create an array of all zeros
print(a)              # Prints "[[ 0.  0.]
                      #          [ 0.  0.]]"

b = np.ones((1,2))    # Create an array of all ones
print(b)              # Prints "[[ 1.  1.]]"

c = np.full((2,2), 7)  # Create a constant array
print(c)               # Prints "[[ 7.  7.]
                       #          [ 7.  7.]]"

d = np.eye(2)         # Create a 2x2 identity matrix
print(d)              # Prints "[[ 1.  0.]
                      #          [ 0.  1.]]"

e = np.random.random((2,2))  # Create an array filled with random values
print(e)                     # Might print "[[ 0.91940167  0.08143941]
                             #               [ 0.68744134  0.87236687]]"
```

```python
import numpy as np

# Create the following rank 2 array with shape (3, 4)
# [[ 1  2  3  4]
#  [ 5  6  7  8]
#  [ 9 10 11 12]]
a = np.array([[1,2,3,4], [5,6,7,8], [9,10,11,12]])

# Use slicing to pull out the subarray consisting of the first 2 rows
# and columns 1 and 2; b is the following array of shape (2, 2):
# [[2 3]
#  [6 7]]
b = a[:2, 1:3]

# A slice of an array is a view into the same data, so modifying it
# will modify the original array.
print(a[0, 1])   # Prints "2"
b[0, 0] = 77     # b[0, 0] is the same piece of data as a[0, 1]
print(a[0, 1])   # Prints "77"
```

```python
import numpy as np

# Create the following rank 2 array with shape (3, 4)
# [[ 1  2  3  4]
#  [ 5  6  7  8]
#  [ 9 10 11 12]]
a = np.array([[1,2,3,4], [5,6,7,8], [9,10,11,12]])

# Two ways of accessing the data in the middle row of the array.
# Mixing integer indexing with slices yields an array of lower rank,
# while using only slices yields an array of the same rank as the
# original array:
row_r1 = a[1, :]    # Rank 1 view of the second row of a
row_r2 = a[1:2, :]  # Rank 2 view of the second row of a
print(row_r1, row_r1.shape)  # Prints "[5 6 7 8] (4,)"
print(row_r2, row_r2.shape)  # Prints "[[5 6 7 8]] (1, 4)"

# We can make the same distinction when accessing columns of an array:
col_r1 = a[:, 1]
col_r2 = a[:, 1:2]
print(col_r1, col_r1.shape)  # Prints "[ 2  6 10] (3,)"
print(col_r2, col_r2.shape)  # Prints "[[ 2]
                             #          [ 6]
                             #          [10]] (3, 1)"
```

```python
import numpy as np

a = np.array([[1,2], [3, 4], [5, 6]])

# An example of integer array indexing.
# The returned array will have shape (3,) and
print(a[[0, 1, 2], [0, 1, 0]])  # Prints "[1 4 5]"

# The above example of integer array indexing is equivalent to this:
print(np.array([a[0, 0], a[1, 1], a[2, 0]]))  # Prints "[1 4 5]"

# When using integer array indexing, you can reuse the same
# element from the source array:
print(a[[0, 0], [1, 1]])  # Prints "[2 2]"

# Equivalent to the previous integer array indexing example
print(np.array([a[0, 1], a[0, 1]]))  # Prints "[2 2]"
```

```python
import numpy as np

# Create a new array from which we will select elements
a = np.array([[1,2,3], [4,5,6], [7,8,9], [10, 11, 12]])

print(a)  # prints "array([[ 1,  2,  3],
          #                [ 4,  5,  6],
          #                [ 7,  8,  9],
          #                [10, 11, 12]])"

# Create an array of indices
b = np.array([0, 2, 0, 1])

# Select one element from each row of a using the indices in b
print(a[np.arange(4), b])  # Prints "[ 1  6  7 11]"

# Mutate one element from each row of a using the indices in b
a[np.arange(4), b] += 10

print(a)  # prints "array([[11,  2,  3],
          #                [ 4,  5, 16],
          #                [17,  8,  9],
          #                [10, 21, 12]])
```

```python
import numpy as np

a = np.array([[1,2], [3, 4], [5, 6]])

bool_idx = (a > 2)   # Find the elements of a that are bigger than 2;
                     # this returns a numpy array of Booleans of the same
                     # shape as a, where each slot of bool_idx tells
                     # whether that element of a is > 2.

print(bool_idx)      # Prints "[[False False]
                     #          [ True  True]
                     #          [ True  True]]"

# We use boolean array indexing to construct a rank 1 array
# consisting of the elements of a corresponding to the True values
# of bool_idx
print(a[bool_idx])  # Prints "[3 4 5 6]"

# We can do all of the above in a single concise statement:
print(a[a > 2])     # Prints "[3 4 5 6]"
```

```python
import numpy as np

x = np.array([1, 2])   # Let numpy choose the datatype
print(x.dtype)         # Prints "int64"

x = np.array([1.0, 2.0])   # Let numpy choose the datatype
print(x.dtype)             # Prints "float64"

x = np.array([1, 2], dtype=np.int64)   # Force a particular datatype
print(x.dtype)                         # Prints "int64"
```

```python
import numpy as np

x = np.array([[1,2],[3,4]], dtype=np.float64)
y = np.array([[5,6],[7,8]], dtype=np.float64)

# Elementwise sum; both produce the array
# [[ 6.0  8.0]
#  [10.0 12.0]]
print(x + y)
print(np.add(x, y))

# Elementwise difference; both produce the array
# [[-4.0 -4.0]
#  [-4.0 -4.0]]
print(x - y)
print(np.subtract(x, y))

# Elementwise product; both produce the array
# [[ 5.0 12.0]
#  [21.0 32.0]]
print(x * y)
print(np.multiply(x, y))

# Elementwise division; both produce the array
# [[ 0.2         0.33333333]
#  [ 0.42857143  0.5       ]]
print(x / y)
print(np.divide(x, y))

# Elementwise square root; produces the array
# [[ 1.          1.41421356]
#  [ 1.73205081  2.        ]]
print(np.sqrt(x))
```

```python
import numpy as np

x = np.array([[1,2],[3,4]])
y = np.array([[5,6],[7,8]])

v = np.array([9,10])
w = np.array([11, 12])

# Inner product of vectors; both produce 219
print(v.dot(w))
print(np.dot(v, w))

# Matrix / vector product; both produce the rank 1 array [29 67]
print(x.dot(v))
print(np.dot(x, v))

# Matrix / matrix product; both produce the rank 2 array
# [[19 22]
#  [43 50]]
print(x.dot(y))
print(np.dot(x, y))
```
## 问题
遇到一个问题：
```python
nums = []
        def inorder(root):
            if not root:
                return
            inorder(root.left)
            nums.append(root.val)
            inorder(root.right)
        
        inorder(proot)
```
这段代码不会报错，但是如果：
```python
cnt, ans = 0, -1
        
        def inorder(root):
            if not root:
                return
            inorder(root.left)
            cnt += 1
            if cnt == k:
                ans = root.val
                return
            inorder(root.right)
```
就会报错`UnboundLocalError: local variable 'cnt' referenced before assignment`
不太理解为啥list没事，后来[SO](https://stackoverflow.com/questions/2609518/unboundlocalerror-with-nested-function-scopes)上的回答我大概明白了：
python通过赋值操作来判断是否是局部变量，但是nums的append不是赋值，因此只要不在嵌套函数里赋值都不会报错。

```python
a = dict()
b = {}
c = {'a': 1, 'b': 2}
```