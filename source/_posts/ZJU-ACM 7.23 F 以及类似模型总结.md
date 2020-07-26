---
title: 'ZJU-ACM 7.23 F 以及类似模型总结'
date: 2020-07-26 19:40:40
tags: 
    - NTT优化
mathjax: true
---

在讲此题之前，我们先来看看两个问题：

* n 个点的有标号无向连通图计数（poj1737）
* 左边 n 个点、右边 m 个点的有标号连通二分图计数

我们采取 dp 来解决这一类图的计数问题。

f[n] 表示 n 个点都和点 1 相连的连通图数，g[n] 表示 n 个点的图数

显然 $f[n] = g[n] - \sum\limits_{i = 1}^{n - 1} C(n - 1, i - 1) * f[i] * g[n - i]$

后面这一坨是不连通的图数，为了防止重复计数，我们枚举**与点 1 相连**的块。

那么第二个问题也是一样的套路，f[n, m] 表示左边 n 个点、右边 m 个点都和左边第 1 个点相连的连通图数，g[n, m] 表示左 n 右 m 的图数, $f[n, m] = g[n, m] - \sum\limits_{i = 1}^{n - 1} \sum\limits_{j = 1}^{m} C(n - 1, i - 1) * C(m, j) * f[i, j] * g[n - i, m - j]$

显然上面这两个柿子都是卷积形式，可以用 FTT/NTT 优化。

-----

回到本题！

f[n, m] 表示 n 个点 m 条边的连通图数

$f[n, m] = g[n, m] - \sum\limits_{i = 1}^{n - 1} \sum\limits_{j = 1}^m C(n - 1, i - 1) * f[i, j] * g[n - i, m - j]$

这也是卷积形式，但是 NTT 是 O(n^4 logn) 的。考虑我们重复计算了 DFT 和 IDFT 很多次，保留 f 的 DFT 形式（点值形式），就可以优化到 O(n^4 logn)。据说卡卡常就能过，但是 TLE 了= = 假装 AC

（挂一个[官方题解](https://www.kdocs.cn/l/sfiEGXVVWPTR?f=201)，我没看懂 F 的复杂度分析，，咕咕）

``` c++
#include <bits/stdc++.h>
#pragma GCC optimize(2)
#pragma GCC optimize(3)
#define rep(i, x, y) for (register int i = x; i <= y; i++)
using namespace std;

const int N = 110, M = 2e4 + 10, mod = 998244353, G = 3, G1 = (mod + 1) / G;
typedef long long ll;
ll n, m, lim = 1;
ll fac[M], inv[M], r[M], h[M], f[N][M], g[N][M], c[N][N];

inline ll quick_pow(ll a, ll b) {
    ll ret = 1;
    for (; b; b >>= 1) {
        if (b & 1) ret = ret * a % mod;
        a = a * a % mod;
    } return ret;
}

inline void init() {
    fac[0] = inv[0] = inv[1] = 1;
    rep(i, 1, m) fac[i] = fac[i - 1] * i % mod;
    rep(i, 2, m) inv[i] = (mod - mod / i) * inv[mod % i] % mod;
    rep(i, 2, m) inv[i] = inv[i - 1] * inv[i] % mod;
    int l = 0;
    while (lim < m * 2) lim <<= 1, ++l;
    rep(i, 0, lim - 1) r[i] = (r[i >> 1] >> 1) | ((i & 1) << (l - 1));

    c[0][0] = 1;
    rep(i, 1, n) {
        c[i][0] = 1;
        rep(j, 1, n)
            c[i][j] = (c[i - 1][j] + c[i - 1][j - 1]) % mod;
    }
}

inline ll C(ll n, ll m) {
    return fac[n] * inv[m] % mod * inv[n - m] % mod;
}

inline void NTT(ll a[], int op) {
    rep(i, 0, lim - 1)
        if (i < r[i]) swap(a[i], a[r[i]]);
    for (int mid = 1; mid < lim; mid <<= 1) {
        ll W = quick_pow(op == 1 ? G : G1, (mod - 1) / (mid << 1));
        for (int j = 0; j < lim; j += (mid << 1)) {
            ll w = 1;
            for (int k = 0; k < mid; k++, w = w * W % mod) {
                ll x = a[j + k], y = w * a[j + k + mid] % mod;
                a[j + k] = (x + y) % mod, a[j + k + mid] = (x - y + mod) % mod;
            }
        }
    }
    if (op == -1) {
        ll Inv = quick_pow(lim, mod - 2);
        rep(i, 0, lim - 1) a[i] = a[i] * Inv % mod;
    }
}

int main() {
    scanf("%lld", &n);
    m = n * (n - 1) / 2;
    init();
    rep(i, 1, n) {
        rep(j, 0, i * (i - 1) / 2)
            g[i][j] = C(i * (i - 1) / 2, j);
        NTT(g[i], 1);
    }
    rep(i, 1, n) {
        rep(j, 0, lim - 1) {
            h[j] = 0;
            rep(k, 1, i - 1)
                h[j] = (h[j] + c[i - 1][k - 1] * f[k][j] % mod * g[i - k][j] % mod) % mod;  // 保留 DFT 形式，省一个 log
            f[i][j] = (g[i][j] - h[j] + mod) % mod;
        }
    }
    NTT(f[n], -1);
    rep(i, 0, m) printf("%lld\n", f[n][i]);
    return 0;
}
```