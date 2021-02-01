---
title: '[POJ2182]-Lost Cows'
date: 2018-08-04 20:41:40
tags:
    - 树状数组
    - 二分
mathjax: true
hidden: true
---

https://vjudge.net/problem/POJ-2182

题意：有 n 头奶牛，已知它们的身高是 1 ～ n 且各不相同，但不知道具体身高。现在这 n 头奶牛站成一列，已知第 i 头奶牛前面有 $A_i$ 头比它低，求每头奶牛的身高。

如果最后一头奶牛面前有 $A_n$ 头牛比它高，那么显然它的身高 $H_n = A_n + 1$。

如果倒数第二头奶牛面前有 $A_{n - 1}$ 头牛比它高，那么：

1. 若 $A_{n - 1} < A_n$, 则它的身高 $H_{n - 1} = A_{n - 1} + 1$；
2. 若 $A_{n - 1} \geq A_n$, 则它的身高 $H_{n - 1} = A_{n - 1} + 2$

以此类推，如果第 k 头奶牛前面有 $A_k$ 头比它高，那么它的身高 $H_n$ 是数值 1 ～ n 中第 $A_k + 1$ 小的没有在 $\{H_{k + 1}, H_{k + 2}, ... H_n\}$ 中出现过的数。

在实现方面，我们可以建立一个长度为 n 的01序列 b，起初全部为 1，然后从 n 到 1 倒序扫描每个 $A_i$，对每个 $A_i$ 执行以下两个操作：

1. 查询序列 b 中第 $A_i + 1$ 个 1 在什么位置，这个位置号就是第 i 头奶牛的身高 $H_i$；
2. 把 $b[H_i]$ 减 1（至 0）。

也就是说，我们需要维护一个01序列，支持查询第 k 个 1 的位置（k 为任意整数），以及修改序列中的一个数值。

我们可以用二分实现，通过 query(mid) 即可得到前 mid 个数中有多少个 1，与 k 比较大小，即可确定二分上下届的变化。

单次操作 $O(log^2n)$

code:
``` c++
#include <cstdio>
#include <algorithm>
#include <cstring>
#include <iostream>
#define lowbit(x) (x) & (-(x))
using namespace std;

const int N = 1e4;
int a[N], ans[N], C[N], n;

int query(int x) {
    int ret = 0;
    for (; x; x -= lowbit(x)) ret += C[x];
    return ret;
}

void add(int x, int y) {
    for (; x <= n; x += lowbit(x)) C[x] += y;
}

int solve(int x) {
    int l = 1, r = n, mid, s;
    while (l < r) {
        mid = (l + r) >> 1;
        s = query(mid);
        if (s > x) {
            r = mid - 1;
        } else if (s < x) {
            l = mid + 1;
        } else {
            if (r == mid) {
                if (query(l) == x) return l;
                return r;
            }
            r = mid;
        }
    }
    return l;
}

int main() {
    ios::sync_with_stdio(false);
    cin >> n;
    add(1, 1);
    for (int i = 2; i <= n; i++) {
        cin >> a[i];
        add(i, 1);
    }
    for (int i = n; i > 0; i--) {
        int k = solve(a[i] + 1);
        ans[i] = k;
        add(k, -1);
    }
    for (int i = 1; i <= n; i++) if (C[i]) {
        ans[1] = C[i];
        break;
    }
    for (int i = 1; i <= n; i++) cout << ans[i] << endl;
    return 0;
}
```