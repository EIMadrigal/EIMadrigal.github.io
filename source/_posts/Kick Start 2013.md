---
title: Kick Start 2013
url: kick-start-2013
date: 2021-04-22 09:33:00
description: Google Kick Start
categories: Computer Science
tags: [Algorithm]
---

## Practice Round

 1. 题目大意：给定一堆人名，从上向下扫描，一旦当前值比前一个的字典序小，就将当前值移动到正确的位置，不论移动多远，代价都是1，求代价总和。

和插入排序类似，如果当前值$j$比$j-1$小，将$j$移到前面合适的位置，此时前$j$个数是局部有序的。这道题只要求出代价和即可，不需要输出排序后的结果，不需要真正去移动，只要记录前$j$个的最大值，如果$j+1$比最大值小，那么必然触发一次移动。举例：  
2 1 5 3 0 j=1, max=2, cost++  
1 2 5 3 0 j=3, max=5, cost++  
1 2 3 5 0 j=0, max=5, cost++  
0 1 2 3 5  
有2个地方要注意：`cin`读入`string`时，会把空格/回车作为分隔符，遇到即停止，所以要用`getline()`，默认以回车结束；`cin`读完`int`后，换行符`\n`仍然在输入流里，所以下一次的`getline`会先读`\n`，故用`cin.get()`先取走`\n`。

```cpp
#include <iostream>
#include <vector>
#include <string>

using namespace std;

int main(int argc, char** argv) {
    int T, N;
    cin >> T;
    for (int i = 0; i < T; ++i) {
        cin >> N;
        cin.get();
        vector<string> names(N);
        for (int j = 0; j < N; ++j) {
            getline(cin, names[j]);
        }
        
        string curMax;
        int money = 0;
        for (int j = 0; j < N; ++j) {
            if (j == 0) {
                curMax = names[0];
                continue;
            }
            if (names[j].compare(curMax) < 0) {
                ++money;
            }
            else {
                curMax = names[j];
            }
        }
        cout << "Case #" << i + 1 << ": " << money << endl;
    }
    return 0;
}
```
 2. 题目大意：
