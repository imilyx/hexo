---
title: '[USACO2009open]-干草堆tower'
date: 2019-09-25 23:30:11
tags:
    - 单调队列
mathjax: true
---

[传送门](https://www.lydsy.com/JudgeOnline/problem.php?id=1233)

是一道非常神奇的单调队列优化DP！

首先，我们发现这题非常不好做（没有入手点，但发现可以用二维 n^3 DP：$f[i, j]$ 表示 i ~ j - 1 是 j 左边的一段的最大高度。我们想想怎么优化。

zkw大神证出，最优的方案必定底层宽度最小。大概是这样证的：有 A 和 B 两个塔，A 的高度最大，B 按从低到高的顺序的每层宽度最小。那么 A 的底层宽度必然大于等于 B，B 的高度必然小于等于 A。必定存在一个位置 pos，该位置 A 的宽度小于 B（因为 A 不可能层层都比 B 宽），那么对于 B 来说，只要把 pos 以上的部分换成 A 塔中 pos 以上的部分，就能变成更优的方案。

如果从底层到顶层 DP，会发现状态不好设计（反正我没想出来qvq），而且没有单调性（比如 n = 3，w = {2, 1, 4}，前两个最大高度是 2，加入第三个就变成 1 了。。），而从顶层到底层 DP 是有单调性的（自己想一想）。

设 f[i] 表示该层编号最小为 i 时的宽度。 $f[i] = \min\limits_{sum[j - 1] - sum[i - 1] >= f[j]}\{sum[j - 1] - sum[i - 1]\}$

即 $sum[j - 1] - f[j] >= sum[i - 1]$

显然只有 $sum[j - 1] - f[j] >= sum[i - 1]$ 且离 i 最近的 j 才能转移给 i。维护一个 $sum[j - 1] - f[j]$ 单调递减（因为 i 从 n 到 1，$sum[i - 1]$ 是递减的）的单调队列就可以了。

山路十八弯~ 思维难度还是很大的！【单调队列难.jpg】

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 1e5 + 10;
typedef long long ll;
ll n, a[N], q[N], f[N], g[N];

int main() {
    cin >> n;
    rep(i, 1, n) scanf("%lld", &a[i]), a[i] += a[i - 1];
    a[n + 1] = a[n];
    int l = 1, r = 1;
    q[l] = n + 1;
    for (int i = n; i >= 1; i--) {
        while (l < r && a[q[l + 1] - 1] - f[q[l + 1]] >= a[i - 1]) ++l;
        f[i] = a[q[l] - 1] - a[i - 1];
        g[i] = g[q[l]] + 1;
        while (l <= r && a[q[r] - 1] - f[q[r]] <= a[i - 1] - f[i]) --r;
        q[++r] = i;
    }
    printf("%lld\n", g[1]);
    return 0;
}
```