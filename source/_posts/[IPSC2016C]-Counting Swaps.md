---
title: '[IPSC2016C]-Counting Swaps'
date: 2018-08-07 18:50:40
tags: 
    - 排列组合
mathjax: true
---

https://www.luogu.org/problemnew/show/P4778#sub

题意：给定一个 1 ～ n 的排列 $p_1, p_2, ... p_n$，可进行若干次操作，每次选择两个整数 x，y，交换 $p_x, p_y$。设把 $p_1, p_2, ... p_n$ 变成单调递增的排列 1, 2, ... n 至少需要 m 次交换，求有多少种操作方法可以只用 m 次交换达到上述目标。答案对 1e9 + 9 取模。

对于一个排列，如果从每个 i 到 $p_i$ 连一条边，那么可以得到一张 n 个点 n 条边的图，且由若干个环组成。

可以证明，把一个长度为 n 的环变成 n 个自环，至少需要 n - 1 次交换。

证明：
首先，把一个长度为 2 的环变成 2 个自环显然需要一次。假设 $\forall k \leq n - 1$，把长度不超过 k 的环变成 k 个自环最少需要 k - 1 次操作。当 k = n 时，把该环拆成长度为 j - i 和 n - (j - i) 的两个环(i < j < n) 。把二者分别拆成自环所需的最小交换次数为 (j - i - 1) + (n - (j - i) - 1) = n - 2，再加上第一次的拆开，共需 n - 1. 通过数学归纳法可知，原命题成立。

证毕。

设 $F_n$ 表示用最少的步数把一个长度为 n 的环变成 n 个自环，共有多少种操作方法。由上证明可知，可以将长度为 n 的环拆成长度为 x 和 y 的两个环，其中 x + y = n。设 T(x, y) 表示有多少种交换方法可以把长度为 n 的环变成长度为 x 和 y 的两个环，容易发现，T(x, y) 在 n 是偶数且 x = y 时是 n / 2, 在 n 是奇数或 $x \neq y$ 时等于 n。

两者变为自环的步数为 x - 1 和 y - 1.

根据多重集的排列数、加法原理和乘法原理：

$$F_n = \sum_{x + y = n} T(x, y) * F_x * F_y * \frac{(n - 2)!}{(x - 1)!(y - 1)!}$$

如果最初的排列 $p_1, p_2, ... p_n$ 由长度为 $l_1, l_2, ... l_k$ 的 k 个环构成，其中 $l_1 + l_2 + ... + l_k = n$，那么最终的答案就是：

$$F_{l_1} * F_{l_2} * ... * F_{l_k} * \frac{(n - k)!}{(l_1 - 1)!(l_2 - 1)! * ... * (l_k - 1)!}$$

1e9 + 9 是质数，可以用乘法逆元处理公式中的除法。$O(n^2)$。

事实上，我们通过找规律可以发现通项公式 $F_n = n^{n - 2}$，从而优化到 $O(n\ log\ n)$。

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 1e5 + 5, mod = 1e9 + 9;
typedef long long ll;
int n, lnk[N], cnt, to[N << 1], nxt[N << 1], T, colo[N], d, F[N];
ll Fac[N], ans;

void add(int u, int v) {
    to[++cnt] = v, nxt[cnt] = lnk[u], lnk[u] = cnt;
}

void dfs(int x, int color) {
    colo[x] = color;
    for (int i = lnk[x]; i; i = nxt[i]) {
        int y = to[i];
        if (!colo[y]) dfs(y, color);
    }
}

ll quick_pow(ll b, ll p) {
    if (!b || p < 0) return 1;
    ll ret = 1;
    for (; p; p >>= 1) {
        if (p & 1) ret = ret * b % mod;
        b = b * b % mod;
    } return ret;
}

int main() {
    ios::sync_with_stdio(false);
    cin >> T;
    Fac[0] = 1;
    for (int i = 1; i <= 100000; i ++) Fac[i] = Fac[i - 1] * i % mod;
    while (T--) {
        memset(F, 0, sizeof(F));
        memset(lnk, 0, sizeof(lnk));
        memset(colo, 0, sizeof(colo));
        cnt = d = 0;
        cin >> n;
        for (int i = 1; i <= n; i++) {
            int u;
            cin >> u;
            add(i, u), add(u, i);
        }
        for (int i = 1; i <= n; i++)
            if (!colo[i]) dfs(i, ++d);
        for (int i = 1; i <= n; i++) F[colo[i]]++;
        ans = 1;
        for (int i = 1; i <= d; i++)
            ans = ans * quick_pow(F[i], F[i] - 2) % mod;
        ans = ans * Fac[n - d] % mod;
        for (int i = 1; i <= d; i++)
            ans = ans * quick_pow(Fac[F[i] - 1], mod - 2) % mod;
        cout << ans << endl;
    }
    return 0;
}
```