---
title: Simple Math Problems
url: simple-math-problems
date: 2019-04-10 22:32:00
description: 基础数学问题
categories: Math
tags: [Algorithm]
---

## 最大公约数 & 最小公倍数

Euclid's Algorithm：若$b\neq0$，那么$gcd(a,b)=gcd(b,a\%b)$  
若$a<b$，定理会先交换a和b. 0和任意正整数a的gcd是a
```cpp
int gcd(int a, int b) {
    return !b ? a : gcd(b, a % b);
}

int gcd(vector<int> nums) {
    int res = nums[0];
    for (int num : nums) {
        res = gcd(num, res);
    }
    return res;
}
```

时间复杂度$O(lgb)$，因为每次递归问题规模都会缩减一半以上。  
最小公倍数$lcm=\frac{a*b}{gcd}$

 - 扩展欧几里得算法

可以计算出满足下式的三元组$(d,x,y)$：
$$d = GCD(a, b) = ax + by$$
```c
int extendEuclid(int a, int b, int* x, int* y) {
    if (0 == b) {
		*x = 1, *y = 0;
		return a;
	}
	int gcd = extendEuclid(b, a % b, x, y);
	int temp = *x;
	*x = *y;
	*y = temp - (*y) * (a / b);
	return gcd;
}
```
简单证明：  
$b=0$是递归基，易得一组解$x=1,y=0$;  
$b \neq0$时：  
首先递归求解：  
$$d'=gcd(b,a\%b)=bx'+(a\%b)y' \ \ \ \ \  \ \ \ \ \ \ \ \ \ (1)$$
我们知道：
$$d=gcd(a,b)=d'=gcd(b,a\%b)\ \ \ \ \ \ \ \ \ \ \ \ \ (2)$$
$$a\%b=a-b*\biggl\lfloor a/b \biggr\rfloor\ \ \ \ \ \ \ \ \ \ \ (3)$$
将(2)(3)式带入(1)：
$$d=bx'+(a-b\biggl\lfloor a/b \biggr\rfloor)y'=ay'+b(x'-\biggl\lfloor a/b \biggr\rfloor y')$$
所以，令$x=y'$, $y=x'-\biggl\lfloor a/b \biggr\rfloor y'$，就可以满足$d=ax+by$

## 素数

 - 判断素数
```cpp
bool isPrime(int n) {
    if (n < 2)  // 1不是素数，也不是合数
        return false;
    int square_root = sqrt(n);
    for (int i = 2; i <= square_root; ++i) {
        if (n % i == 0)
            return false;
    }
    return true;
}
```
 - 打素数表

第一种方法是枚举判断, $O(n^{1.5})$.  
第二种是Eratosthenes筛法，复杂度$O(nlog\ logn)$.
![image](https://img-blog.csdnimg.cn/6187ce5f6b95419d907f6e8d094d163f.png)

```cpp
bool isprime[100005];
vector<int> primes;
void seive() {
    std::fill(isprime, isprime + 100005, true);
    isprime[0] = false, isprime[1] = false;
    for (int i = 2; i * i < 100005; ++i) {
        if (isprime[i]) {
            for (int j = i * i; j < 100005; j += i) {
                isprime[j] = false;
            }
        }
    }
    for (int i = 0; i < 100005; ++i) {
        if (isprime[i]) {
            primes.push_back(i);
        }
    }
}
```

进一步的优化是欧拉筛，复杂度$O(n)$
```cpp
bool isprime[maxn];
vector<int> primes;
void seive(int n) {
    std::fill(isprime, isprime + n + 1, true);
    for (int i = 2; i <= n; ++i) {
        if (isprime[i])
            primes.push_back(i);
        for (int p : primes) {
            if (p * i > n)
                break;
            isprime[p * i] = false;
            if (i % p == 0)
                break;
        }
    }
}
```

## 分解质因子

每个数都可以分解为质数的乘机, 注意1要特判.

枚举小于等于sqrt(n)内的所有质因子, 判断哪个是n的因子.

```cpp
vector<pair<int, int>> primeBreak(int n) {
    vector<pair<int, int>> facs;
    for (int i = 0; primes[i] * primes[i] <= n; i++) {
        while (n % primes[i] == 0) {
            if (!facs.empty() && facs.back().first == primes[i]) {
                facs.back().second += 1;
            } else {
                facs.push_back({primes[i], 1});
            }
            n /= primes[i];
        }
    }
    if (n > 1) {
        facs.push_back({n, 1});
    }
    return facs;
}
```

## 分数

PAT甲1088是比较经典的分数处理问题，求2个分数的和、差、积、商，输出最简形式。  
表示、化简、运算、输出，代码阐释得很清楚。
```cpp
#include <cstdio>
#include <algorithm>

using namespace std;

typedef long long ll;

ll gcd(ll a,ll b) {
        return !b ? a : gcd(b,a % b);
}

struct Fraction {
        ll nume,deno;
};

Fraction reduction(Fraction a) {
        if(a.deno < 0) {
                a.deno = -a.deno;
                a.nume = -a.nume;
        }
        if(a.nume == 0) {
                a.deno = 1;
        }
        else {
               int d = gcd(abs(a.nume),abs(a.deno));
               a.nume /= d;
               a.deno /= d;
        }
        return a;
}

Fraction add(Fraction a,Fraction b) {
        Fraction res;
        res.deno = a.deno * b.deno;
        res.nume = a.deno * b.nume + a.nume * b.deno;
        return reduction(res);
}

Fraction sub(Fraction a,Fraction b) {
        Fraction res;
        res.deno = a.deno * b.deno;
        res.nume = a.nume * b.deno - a.deno * b.nume;
        return reduction(res);
}

Fraction times(Fraction a,Fraction b) {
        Fraction res;
        res.deno = a.deno * b.deno;
        res.nume = a.nume * b.nume;
        return reduction(res);
}

Fraction divide(Fraction a,Fraction b) {
        Fraction res;
        res.deno = a.deno * b.nume;
        res.nume = a.nume * b.deno;
        return reduction(res);
}

void showFrac(Fraction a) {
        a = reduction(a);
        if(a.nume < 0) {
                printf("(");
        }
        if(a.deno == 1) {
                printf("%lld",a.nume);
        }
        else if(abs(a.nume) > abs(a.deno)) {
                printf("%lld %lld/%lld",a.nume / a.deno,abs(a.nume) % a.deno,a.deno);
        }
        else {
                printf("%lld/%lld",a.nume,a.deno);
        }
        if(a.nume < 0) {
                printf(")");
        }
}

int main() {
        Fraction a,b;
        scanf("%lld/%lld%lld/%lld",&a.nume,&a.deno,&b.nume,&b.deno);

        showFrac(a);
        printf(" + ");
        showFrac(b);
        printf(" = ");
        showFrac(add(a,b));
        printf("\n");

        showFrac(a);
        printf(" - ");
        showFrac(b);
        printf(" = ");
        showFrac(sub(a,b));
        printf("\n");

        showFrac(a);
        printf(" * ");
        showFrac(b);
        printf(" = ");
        showFrac(times(a,b));
        printf("\n");

        showFrac(a);
        printf(" / ");
        showFrac(b);
        printf(" = ");
        if(b.nume == 0) {
                printf("Inf\n");
        }
        else {
                showFrac(divide(a,b));
                printf("\n");
        }
        return 0;
}
```

## Reference
[素数筛](https://zhuanlan.zhihu.com/p/100051075)
