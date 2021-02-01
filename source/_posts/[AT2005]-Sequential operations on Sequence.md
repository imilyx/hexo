---
title: "[AT2005]-Sequential operations on Sequence"
date: 2020-02-11 23:15:40
tags: 
    - 思维
mathjax: true 
---

又是道大好题，，

* 思维题，概括不了

1. 这步必须想到，是根基。对照样例应该也能发现这个性质：用单调栈将 $q$ 变成上升的序列，**第 $i$ 步操作后得到的序列就是第 $i - 1$ 步操作得到序列的几倍 + 前缀**
2. 见过推土机吗？这题实现起来就是这样。设 $f[i]$ 表示第 $i$ 步操作后的序列在最终序列中出现的次数，显然 $f[i] = f[i + 1] * (len[i + 1] / len[i])$，余数部分找循环节，诸如此类，递规处理。不断被肢解，最后剩下的一小撮没有循环节了，那一小撮长这样：1 2 3 4 ... 也就是初始序列的前缀，提前累计到 ans[] 里
3. 复杂度分析：余数部分处理，每次对一个比自己小的循环节长度取模，至少减小一半，即 $logn$; 二分找小于余数部分长度的最大循环节，$logn$，所以最后是 $O(n log^2 n)$

其实想想，就好像一个序列不断被打乱，把它复原就可以了。。很抽象了

code :
``` c++
// 有没有思路都先分析完样例！！！说不定会有启发！！！
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

typedef long long ll;
const int N = 1e5 + 10;
ll n, Q, m, q[N], x, f[N], ans[N];

void solve(ll x, ll v) {
    int k = upper_bound(q + 1, q + m + 1, x) - q - 1;
    if (!k) {
        ans[1] += v, ans[x + 1] -= v;  // 提前累计
        return;
    }
    f[k] += v * (x / q[k]), solve(x % q[k], v);
}

int main() {
    cin >> n >> Q;
    q[++m] = n;
    rep(i, 1, Q) {
        scanf("%lld", &x);
        while (m && q[m] >= x) --m;
        q[++m] = x;
    }

    f[m] = 1;
    for (int i = m; i >= 2; i--)
        f[i - 1] += f[i] * (q[i] / q[i - 1]), solve(q[i] % q[i - 1], f[i]);

    ans[1] += f[1], ans[q[1] + 1] -= f[1];
    rep(i, 1, n) ans[i] += ans[i - 1], printf("%lld\n", ans[i]);
    return 0;
}
```