---
title: Fibonacci Sequence
url: fibonacci-sequence
date: 2018-02-07 19:21:32
description: 斐波那契数列
categories: Math
tags: [Linear Algebra]
---

## 递归
斐波那契数列定义：
$$
F(n)=\left\{\begin{matrix}0, n=0\\
1, n=1\\
F(n-1)+F(n-2), n>1\end{matrix}\right.
$$
递归解法最直观，但是复杂度也最高：$O(2^n)$
```c
int Fibonacci(int n)
{
    if (n <= 0) //细节可以处理非法输入
        return 0;
    else if (1 == n)
        return 1;
    return Fibonacci(n - 1) + Fibonacci(n - 2);
}
```
为了避免重复计算，可以将每一步计算得到的$F(i)$存起来，这样的话时间复杂度降为$O(n)$，但空间复杂度升为$O(n)$。

## 通项
求解通项的方法有好几种，下面展示一种用线性代数求解的方法：  
斐波那契数列的递推公式是二阶差分方程，先用一点小技巧将其化为一阶：
$$
\begin{cases}F_{k+2}=F_{k+1}+F_{k}\text{}\\F_{k+1}=F_{k+1}\text{}\\\end{cases}
$$
我们令$u_k=\begin{bmatrix}F_{k+1}\\F_{k}\\\end{bmatrix}$，那么$u_{k+1}=\begin{bmatrix}F_{k+2}\\F_{k+1}\\\end{bmatrix}=\begin{bmatrix}1\ 1\\1\ 0\\\end{bmatrix}u_k$。  
矩阵$A=\begin{bmatrix} 1\ 1\\1\ 0\\\end{bmatrix}$，令$det(A-\lambda I)=\lambda^2-\lambda-1=0$，求得$\lambda=\frac{1\pm \sqrt5}{2}$，对应于两个特征值的特征向量为$x_1=\begin{bmatrix}   \lambda_1\\   1\\  \end{bmatrix},x_2=\begin{bmatrix}   \lambda_2\\   1\\  \end{bmatrix}$。  
求得特征值和特征向量后，我们将$u_0=\begin{bmatrix}   F_1\\   F_0\\  \end{bmatrix}=\begin{bmatrix}   1\\   0\\  \end{bmatrix}=c_1x_1+c_2x_2$，解得$c_1=-\frac{1}{\sqrt5}, c_2=\frac{1}{\sqrt5}$  
故$u_k=S\Lambda^{k}c=\begin{bmatrix} c_1\lambda_1^{k+1}+c_2\lambda_2^{k+1}\\c_1\lambda_1^{k}+c_2\lambda_2^{k}\\\end{bmatrix}$  
所以通项公式可以表示为$F(n)=C_1\lambda_1^n+C_2\lambda_2^n$。  
故斐波那契数列的通项公式为：$F(n)=\frac{1}{\sqrt5}[(\frac{1+\sqrt5}{2})^n-(\frac{1-\sqrt5}{2})^n]$  
用公式求解的复杂度为$O(1)$，但是由于无理数在计算机中的存储不是精确的，所以结果的精度很难保证。

## 分治
通过矩阵形式的递推：
$$\begin{bmatrix}F(n)\\ F(n-1)\end{bmatrix}=\begin{bmatrix}1\  1\\ 1\  0\end{bmatrix}\begin{bmatrix}F(n-1)\\ F(n-2)\end{bmatrix}$$
不断向下递推，可以得到：
$$\begin{bmatrix}F(n)\\ F(n-1)\end{bmatrix}={\begin{bmatrix}1\  1\\ 1\  0\end{bmatrix}}^{n-1}\begin{bmatrix}F(1)\\ F(0)\end{bmatrix}$$
接下来就是求解矩阵的高次方，通过快速幂可以在$O(logn)$时间内进行计算：  
整数的快速幂代码：
```cpp
int qpow(int a, int n) {
    int res = 1;
    while (n) {
        if (n & 1)
            res *= a;
        a *= a;
        n >>= 1;
    }
    return res;
}

int qpow(int a, int n) {
    if (n == 0)
        return 1;
    int half = qpow(a, n / 2);
    if (n % 2)
        return a * half * half;
    else
        return half * half;
}
```

将传入的参数改为矩阵，乘法改为矩阵乘法，就可以得到矩阵快速幂：  
以二阶矩阵为例，求解斐波那契数列：
```c
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>

using namespace std;

struct Matrix {
    int a[2][2];
}base,ans;

Matrix multi(Matrix a, Matrix b)
{
    Matrix res;
    for (int i = 0; i < 2; i++) //第i行
    {
        for (int j = 0; j < 2; j++)  //第j列
        {
            res.a[i][j] = 0;
            for (int k = 0; k < 2; k++)
                res.a[i][j] += a.a[i][k] * b.a[k][j];
        }
    }

    return res;
}

Matrix QuickPow(int n)
{
    base.a[0][0] = base.a[0][1] = base.a[1][0] = 1;
    base.a[1][1] = 0;   //初始化矩阵

    //结果矩阵初始化为单位阵
    ans.a[0][0] = ans.a[1][1] = 1;
    ans.a[1][0] = ans.a[0][1] = 0;

    while (n)
    {
        if (n & 1)
        {
            ans = multi(ans, base);
        }
        base = multi(base, base);
        n >>= 1;
    }

    return ans;
}

int main()
{
    int n;
    cin >> n;

    QuickPow(n);
    cout << ans.a[1][0] << endl;

    return 0;
}
```

## 动态规划
```c
int Fibonacci(int n) {
    int a = 0, b = 1;
    int ans = 0;
    for(int i = 0;i < n;++i) {
        ans = a + b;
        a = b;
        b = ans;
    }
    return ans;
}
```

## Refs
[斐波那契数列](https://www.zhihu.com/question/28062458/answer/39763094)
