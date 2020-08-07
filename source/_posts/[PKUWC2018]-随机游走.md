---
title: '[PKUWC2018]-随机游走'
date: 2020-08-06 15:18:40
tags: 
    - 概率与期望
    - minmax容斥
    - 集合幂级数
mathjax: true
---

终于来搞这个早就想搞的题了！！著名（雾）的 $minmax$ 题

据说可以[不用 $minmax$ 容斥](https://www.cnblogs.com/Mychael/p/9229291.html)，设 $f[i, S]$ 表示从 $i$ 出发走完 $S$ 集合的期望 + 把 dp 柿子表示成 $Ax + B$ 的形式就可以做到 $O(n2^n)$ 的复杂度。。。大同小异吧

考虑 $minmax$ 容斥，设 $f[i]$ 为从 $i$ 点出发，到达集合 $S$ 中第一个点的期望时间，那么只要计算 $f[x]$ 就好了。

本题是树的形态，可以推得：

* $x \in S$, $f[x] = 0$
* $x \notin S$, $f[x] = \frac{f[fa_x]}{deg_x} + (\sum\limits_{y \in Son(x)}\frac{f[y]}{deg_x}) + 1$

发现父亲和儿子很不好搞，而每个节点只有一个父亲，我们就用到一个套路：将 $f[x]$ 表示为 $A * f[fa] + B$ 的形式

从下往上做，推得：$A_x = \frac{1}{deg_x - \sum A_y}$, $B_x = \frac{\sum B_y + deg_x}{deg_x - \sum A_y}$

复杂度 $O(n2^n)$。可以用 FMT 或者 FWT_and 做容斥。

``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int mod = 998244353, M = 20, N = (1 << 18);
typedef long long ll;
ll n, Q, st, S;
ll f[N], g[N], deg[M], a[M], b[M];
int to[M << 1], nxt[M << 1], lnk[M], cnt;

void add(int x, int y) {
    to[++cnt] = y, nxt[cnt] = lnk[x], lnk[x] = cnt;
}

ll quick_pow(ll a, ll b) {
    ll ret = 1;
    for (; b; b >>= 1) {
        if (b & 1) ret = ret * a % mod;
        a = a * a % mod;
    } return ret;
}

void dfs(int x, int fa) {
    if ((S >> (x - 1)) & 1) {
        a[x] = b[x] = 0; return;
    }
    a[x] = b[x] = deg[x];
    for (int i = lnk[x]; i; i = nxt[i]) {
        int y = to[i];
        if (y == fa) continue;
        dfs(y, x);
        a[x] = (a[x] - a[y] + mod) % mod;
        b[x] = (b[x] + b[y]) % mod;
    }
    a[x] = quick_pow(a[x], mod - 2);
    b[x] = a[x] * b[x] % mod;
}

void FWT_or() {
    for (int k = 1; k < (1 << n); k <<= 1) {
        for (int i = 0; i < (1 << n); i += (k << 1))
            for (int j = 0; j < k; ++j)
                f[i + j + k] = (f[i + j + k] + f[i + j] + mod) % mod;
    }
}

void FMT() {
    rep(i, 1, n)
        rep(j, 0, (1 << n) - 1)
            if ((j >> (i - 1)) & 1)
                f[j] = (f[j] + f[j - (1 << (i - 1))]) % mod;
}

int main() {
    cin >> n >> Q >> st;
    rep(i, 1, n - 1) {
        int x, y; scanf("%d%d", &x, &y);
        add(x, y), add(y, x);
        ++deg[x], ++deg[y];
    }
    rep(s, 1, (1 << n) - 1) {
        S = s; dfs(st, 0);
        f[s] = (__builtin_popcount(s) & 1) ? b[st] : mod - b[st];
    }
    // FWT_or();  // 两个等价的。都可以。
    FMT();
    while (Q--) {
        int k, x, bit = 0; scanf("%d", &k);
        while (k--)
            scanf("%d", &x), bit |= (1 << (x - 1));
        printf("%lld\n", f[bit]);
    }
    return 0;
}
```