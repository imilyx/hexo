---
title: '[POJ1958]-Strange Towers of Hanoi'
date: 2018-05-13 20:42:40
tags:
    - 递推与递归
hidden: true
---

递推算法。
3座塔的经典Hanoi应该很好求：设 d[n] 表示 n 个盘子的步数，则 d[n] = 2 * d[n - 1] + 1。
此题解法为，设 f(n) 表示4座塔情况下移动 n 个盘子的步数，则 f(n) = min(2 * f(i) + d[n - i])。当然也可以打表找规律。

CODE：
``` c++
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>
using namespace std;

int d[15], f[15];

int main() {
    memset(f, 0x3f, sizeof(f));
    f[1] = 1;
    for (int i = 1; i <= 12; i++) d[i] = d[i - 1] * 2 + 1;
    for (int i = 2; i <= 12; i++)
        for (int j = 1; j < i; j++)
            f[i] = min(f[i], f[j] * 2 + d[i - j]);
    for (int i = 1; i <= 12; i++) printf("%d\n", f[i]);
    return 0;
}
```