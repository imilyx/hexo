---
title: '[POJ3696]-The Luckiest Number'
date: 2018-07-25 15:50:40
tags: 
    - 同余方程
mathjax: true
hidden: true
---

https://vjudge.net/problem/POJ-3696

题意：给定一个正整数 L，$L \leq 2 * 10^9$。问至少多少个 8 连在一起组成的正整数是 L 的倍数。

x 个 8 连在一起可写作 $8(10^x - 1) / 9$。题目就是让我们求出一个最小的 x，满足 $L | 8(10^x - 1) / 9$ 。设 d = gcd(L, 8) 。

通过移项得到，$10^x \equiv 1 (mod\ \frac{9L}{d})$ 。

引理：若正整数 a, n 互质，则满足 $a^x \equiv 1(mod\ n)$ 的最小正整数 $x_0$ 是 $\phi(n)$ 的约数。

证明：反证法。假设满足 $a^x \equiv 1(mod\ n)$ 的最小正整数 $x_0$ 不能整除 $\phi(n)$。

设 $\phi(n) = qx_0 + r(0 < r  < x_0)$ 。因为欧拉定理 $a^{\phi(n)} \equiv 1(mod\ n)$ ，所以 $a^r \equiv 1 (mod\ n)$ 。这与 $x_0$ 最小矛盾。故假设不成立，原命题成立。

证毕。

根据以上引理，我们只需求出欧拉函数 $\phi(9L/d)$ ，枚举它的所有约数，用快速幂逐一检查是否满足条件即可。时间复杂度 $O(\sqrt{L}\ log\ L)$ 。

关于同余的题目很可能会涉及费马小定理与欧拉定理，这些务必掌握。

CODE：
``` c++
#include <iostream>
#include <algorithm>
#include <cstdlib>
#include <cstdio>
#include <cstring>
using namespace std;
#define maxn (1 << 16)

typedef long long ll;
ll n, factor[maxn];

ll euler(ll n) {
    ll ret = n;
    for (ll i = 2; i * i <= n; i++)
        if (n % i == 0) {
            ret = ret / i * (i - 1);
            while (n % i == 0)
                n /= i;
        }
    if (n > 1)
        ret = ret / n * (n - 1);
    return ret;
}

ll mult(ll a, ll b, ll p) {
    ll ret = 0;
    while (b) {
        if (b & 1)
            ret = (ret + a) % p;
        a = 2 * a % p;
        b >>= 1;
    }
    return ret;
}

ll power(ll x, ll n, ll p) {
    ll ret = 1;
    x %= p;
    while (n) {
        if (n & 1)
            ret = mult(ret, x, p);
        x = mult(x, x, p);
        n >>= 1;
    }
    return ret;
}

ll work() {
    n *= 9;
    for (int i = 0; i < 3; i++)
        if (n % 2 == 0)
            n /= 2;
        else
            break;
    ll p = n;
    if (p % 2 == 0 || p % 5 == 0)
        return 0;
    ll phi = euler(p);
    int cnt = 0;
    for (ll i = 1; i * i <= phi; i++)
        if (phi % i == 0) {
            factor[cnt++] = i;
            factor[cnt++] = phi / i;
        }
    sort(factor, factor + cnt);
    for (int i = 0; i < cnt; i++)
        if (power(10, factor[i], p) == 1)
            return factor[i];
    return 0;
}

int main() {
    int t = 0;
    while (scanf("%lld", &n), n) {
        t++;
        printf("Case %d: ", t);
        printf("%lld\n", work());
    }
    return 0;
}
```