---
title: '[luogu1365]-WJMZBMR打osu!'
date: 2018-11-01 22:58:40
tags: 
    - 概率与期望
mathjax: true
---

[题目链接](https://www.luogu.org/problemnew/show/P1365)

期望DP！

设 f[i] 表示到了第 i 位的总期望，g[i] 表示到了第 i 位结尾的连续 o 的期望长度，那么：

1. 当 s[i] = x, 则 f[i] = f[i - 1], g[i] = 0.
2. 当 s[i] = o, 则 f[i] = f[i - 1] + 2g[i - 1] + 1, g[i] = g[i - 1] + 1(这一步可以根据完全平方公式推出)
3. 当 s[i] = ?, 则 f[i] = f[i - 1] + g[i - 1] + 0.5, g[i] = (g[i - 1] + 1) / 2.

我们发现转移时的状态只与上一轮的状态有关，所以可以使用滚动数组节省空间。

``` c++
#include <bits/stdc++.h>
using namespace std;

int n, now;
char ch;
double f[2], g[2];

int main() {
    cin >> n;
    for (int i = 1; i <= n; i++) {
        cin >> ch;
        if (ch == 'x') f[now ^ 1] = f[now], g[now ^ 1] = 0;
        else if (ch == 'o') f[now ^ 1] = f[now] + 2 * g[now] + 1, g[now ^ 1] = g[now] + 1;
        else f[now ^ 1] = f[now] + g[now] + 0.5, g[now ^ 1] = g[now] / 2 + 0.5;
        now ^= 1;
    }
    printf("%.4lf\n", f[now]);
    return 0;
}
```