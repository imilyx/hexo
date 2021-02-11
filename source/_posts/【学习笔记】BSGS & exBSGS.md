---
title: '【学习笔记】BSGS & exBSGS'
date: 2021-02-08 08:40:40
tags:
    - 学习笔记
    - BSGS
mathjax: true
---

## BSGS

$O(\sqrt{p})$ 求解关于 $x$ 的高次同余方程 $a^x \equiv n \pmod{p}$，其中 $p$ 为质数。

由 $a^{\phi(p)} \equiv 1 \pmod{p}$ 得 $x \in [0, p - 1)$。设 $x = i * k - j$, 则 $(a^k)^i \equiv b * a^j \pmod{p}$，取 $k = \sqrt{p}$，对于 $j \in [0, k)$ 把 $b * a^j \pmod{p}$ 存入 hash 表，枚举 $i \in [0, k)$，查找 hash 表中是否有当前的 $(a^k)^i \pmod{p}$。

## exBSGS

$p$ 非质的版本，$(a, p) > 1$。考虑转化为 $(a, p) = 1$。

展开为 $a \cdot a^{x - 1} + p * y = b$，必须要 $(a, p) \mid b$，$a^{x - 1}$ 和 $y$ 才有整数解。

令上柿变为：$a^{x - 1} \cdot \frac{a}{(a, p)} \equiv \frac{b}{(a, p)} \pmod{\frac{p}{(a, p)}}$。

此时如果 $(a^{x - 1}, \frac{p}{(a, p)}) = 1$，就直接用 BSGS 解 $a^{x - 1} \equiv \frac{\frac{b}{(a, p)}}{\frac{a}{(a, p)}} \pmod{\frac{p}{(a, p)}}$（注意这里的模数可能非质，不能用费马小定理求逆元，只能 exgcd 啦）；否则递归分解直到 $(a', p') = 1$。

在递归过程中若出现 $(a', p') ∤ \frac{\frac{b}{(a, p)}}{\frac{a}{(a, p)}}$，直接无解。**注意：有特例：若 $\frac{\frac{b}{(a, p)}}{\frac{a}{(a, p)}} \equiv 1 \pmod{\frac{p}{(a, p)}}$，表示 $a^{x - k} \equiv 1 \pmod{p}$（递归了 $k$ 层），即 $a^{x - k} * a^k \equiv 1 * b \pmod{p}$，$k$ 为解。**

<details>
    <summary>template</summary>
    ``` c++
    // luogu_4195
    #include <bits/stdc++.h>
    #define rep(i, x, y) for (int i = x; i <= y; i++)
    using namespace std;

    typedef long long ll;
    map<ll, ll> mp;
    ll n, m, p, ans;

    ll gcd(ll a, ll b) {
        return !b ? a : gcd(b, a % b);
    }
    ll qpow(ll a, ll b, ll mod) {
        ll ret = 1;
        for (; b; b >>= 1, a = a * a % mod) if (b & 1) ret = ret * a % mod;
        return ret;
    }
    ll exgcd(ll a, ll b, ll &x, ll &y) {
        if (!b) { x = 1, y = 0; return a; }
        ll d = exgcd(b, a % b, y, x); y -= a / b * x; return d;
    }
    ll BSGS(ll a, ll b, ll p, ll t) {
        mp.clear();
        ll k = (ll)sqrt((double)p), x, y;
        exgcd(t, p, x, y), b = (b * x % p + p) % p;
        ll tmp = b, S = qpow(a, k, p);
        rep(i, 0, k) {
            mp[tmp] = i;
            tmp = tmp * a % p;
        }
        tmp = S;
        rep(i, 1, k) {
            if (mp[tmp]) return i * k - mp[tmp];
            tmp = tmp * S % p;
        } return -1;
    }

    ll exBSGS() {
        ll k = 0, d = 1, t = 1;
        if (m == 1) return 0;
        while ((d = gcd(n, p)) > 1) {
            if (m % d) return -1;
            ++k, m /= d, p /= d, t = t * (n / d) % p;
            if (t == m) return k;
        }
        return (d = BSGS(n, m, p, t)) == -1 ? -1 : d + k;
    }

    int main() {
        while (cin >> n >> p >> m && (n || m || p)) {
            n %= p, m %= p, ans = exBSGS();
            if (ans < 0) puts("No Solution");
            else printf("%lld\n", ans);
        }
        return 0;
    }
    ```
</details>