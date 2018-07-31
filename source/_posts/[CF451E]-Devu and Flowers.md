---
title: '[CF451E]-Devu and Flowers'
date: 2018-07-31 22:37:40
tags: 
    - 容斥原理
    - 排列组合
mathjax: true
---

https://vjudge.net/problem/CodeForces-451E

题意：有 n 个盒子，第 i 个盒子里有 $A_i$ 枝花，同一个盒子内的花颜色相同，不同盒子内的花颜色不同。要从这些盒子里选出 m 朵花组成一束，求方案数。若两束花每种颜色的花的数量都相同，即可认为是相同的方案。输出答案对 $10^9 + 7$ 取模后的结果。$1 \leq n \leq 20$, $1 \leq m \leq 10^{14}$, $p \leq A_i \leq 10^{12}$.

如果知道多重集的组合数，这道题就很好理解了。

先普及一个重要定理：设 S = {n1 · a1, n2 · a2, ... nk · ak}，是由 n1 个 a1，... nk 个 ak 组成的多重集，设整数 $r \leq n_i(i \in [1, k])$, 从 S 中取出 r 个元素组成一个多重集（不考虑顺序），组成的不同多重集数量为：

$$C_{k + r - 1}^{k - 1}$$

由于 $r \leq n_i(\forall i \in [1, k])$，所以可以忽略 ni 的限制，将 ni 看作 $\infty$。

但这里就不行了。我们设第 i 个盒子里的花颜色为 $C_i$。

那么本题的答案为：

$$C^{n - 1}_{n + m - 1} - \sum\limits_{i = 1}^n C_{n + m - A_i - 2}^{n - 1} + \sum\limits_{1 \leq i < j \leq n} C_{n + m - A_i - A_j - 3}^{n - 1} - ... + (-1)^n C_{n + m - \sum_{i = 1}^n C_i - (n + 1)}^{n - 1}$$

证明过程具体参见小白书 P169 页。

在实现时，计算组合数可以先计算模 1e9 + 7 的乘法逆元。可以枚举 $x = 0 \sim 2^n - 1$。

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

const int mod = 1e9 + 7;
typedef long long ll;
ll a[22], m, ans;
int inv[22], n;

int quick_pow(int a, int b) {
    int res = 1;
    for (; b; b >>= 1) {
        if (b & 1) res = (ll)res * a % mod;
        a = (ll)a * a % mod;
    } return res;
}

int C(ll y, int x) {
    if (y < 0 || x < 0 || y < x) return 0;
    y %= mod;
    if (y == 0 || x == 0) return 1;
    int ans = 1;
    for (int i = 0; i < x; i++) ans = (ll)ans * (y - i) % mod;
    for (int i = 1; i <= x; i++) ans = (ll)ans * inv[i] % mod;
    return ans;
}

int main() {
    ios::sync_with_stdio(false);
    for (int i = 1; i <= 20; i++) inv[i] = quick_pow(i, mod - 2);
    cin >> n >> m;
    for (int i = 1; i <= n; i++) cin >> a[i];
    for (int x = 0; x < (1 << n); x++) {
        if (x == 0) {
            ans = (ans + C(n + m - 1, n - 1)) % mod;
        } else {
            ll t = n + m;
            int p = 0;
            for (int i = 0; i < n; i++)
                if (x >> i & 1) {
                    p++, t -= a[i + 1];
                }
            t -= p + 1;
            if (p & 1) ans = (ans - C(t, n - 1)) % mod;
            else ans = (ans + C(t, n - 1)) % mod;
        }
    }
    cout << (ans + mod) % mod << endl;
    return 0;
}
```