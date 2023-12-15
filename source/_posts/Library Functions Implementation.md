---
title: Library Functions Implementation
url: library-functions-implementation
date: 2020-08-16 19:35:00
description: 经典库函数
categories: Computer Science
tags: [Algorithm,Interview]
---

```cpp
void* memcpy(const void* src, void* des, unsigned int cnt) {
	if (!src || !des)
		return nullptr;
	if (des > src && (const char*)src + cnt < (char*)des) {
		const char* srcB = (const char*)src + cnt - 1;
		char* desB = (char*)des + cnt - 1;
		while (cnt--) {
			*desB = *srcB;
			--desB;
			--srcB;
		}
	}
	else {
		const char* srcF = (const char*)src + cnt - 1;
		char* desF = (char*)des + cnt - 1;
		while (cnt--) {
			*desF = *srcF;
			++desF;
			++srcF;
		}
	}
	return des;
}
```

```cpp
// 将original中的子串substr替换为replace
char* strReplace(const char* original, const char* substr, const char* replace) {
	if (!original || !substr || !replace) {
		return NULL;
	}

	int len = strlen(original);

	char* newStr = malloc((len + 1) * sizeof(char));
	memcpy(newStr, original, (len + 1) * sizeof(char));

	char* p = strstr(newStr, substr);
	memcpy(p, replace, strlen(replace));

	return newStr;
}
```

## vector
比较高频的问题：为什么`push_back()`的平均时间复杂度是$O(1)$？  
假设倍增因子是$m$，vector当前有$n$个元素，那么扩容过程大致为：$0,1,m,m^2,...,m^{log_mn}$，每次扩容的复杂度等于当时的元素个数，无需扩容时插入的时间复杂度为$O(1)$，所以总的复杂度为：
$$1+m+m^2+...+m^{log_mn}=\frac{mn-1}{m-1}$$
如果$m=2$，那么均摊到$n$个元素，插入每个元素的操作复杂度就是$O(1)$
```cpp
template<typename t="">
class myvector {
public:
	void push_back(T& x) {
		if (size == capacity) {
			broad(2 * capacity + 1);
		}
		obj[size++] = x;
	}
private:
	void broad(int newCap) {
		if (newCap < size)
			return;

		T* tmp = obj;
		obj = new T[newCap];
		for (int i = 0; i < size; ++i) {
			obj[i] = tmp[i];
		}
		delete[] tmp;
	}
	int size;
	int capacity;
	T* obj;
};
```

```cpp
int myAtoi(const char* str) {
	if (!str) {
		throw "Invalid input!";
	}

	while (*str == ' ') {
		++str;
	}

	int sign = 1;
	if (*str == '+' || *str == '-') {
		if (*str == '-') {
			sign = -1;
		}
		++str;
	}
	else if (*str < '0' || *str > '9') {
		throw "Invalid input!";
	}

	int value = 0;
	while (*str != '\0' && *str >= '0' && *str <= '9') {
		value = value * 10 + *str - '0';
		++str;
	}
	return sign * value;
}
```

```cpp
// O(mn)，可以用KMP优化为O(m+n)
class Solution {
public:
    int strStr(string haystack, string needle) {
        if (needle.empty()) {
            return 0;
        }
        
        int i = 0, j = 0;
        while (i < haystack.size() && j < needle.size()) {
            if (haystack[i] == needle[j]) {
                ++i;
                ++j;
            } else {
                i = i - j + 1;
                j = 0;
            }
            
            if (j == needle.size()) {
                return i - needle.size();
            }
        }
        return -1;
    }
};
```