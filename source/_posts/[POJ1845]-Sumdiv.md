---
title: '[POJ1845]-Sumdiv'
date: 2018-05-18 20:13:40
tags: 
    - 分治
mathjax: true
---

https://vjudge.net/problem/POJ-1845

题意：求$A^B$的所有约数之和 mod 9901 ($1 \leq A, B \leq 5 * 10^7$)。

把 A 分解质因数，表示为 $p_1^{c1} * p_2^{c2} * ... * p_n^{cn}$。
那么 $A^B$ 可表示为 $p_1^{B * c1} * p_2^{B * c2} * ... * p_n^{B * cn}$。
$p_1^{k1} * p_2^{k2} * ... * p_n^{kn}$，其中 $0 \leq k_i \leq B * c_i (1 \leq i \leq n)$。

根据乘法分配律、整式乘法及因式分解，$A^B$的所有约数之和就是：

$$(1 + p_1 + p_1^2 + ... + p_1^{B * c_1}) * (1 + p_2 + p_2^2 + ... + p_2^{B * c_2}) * ... * (1 + p_n + p_n^2 + ... + p_n^{B * c_n})$$。

那么我们的任务就是对于等比数列 $1 + p + ... + p^c$ 求和。然而朴素算法(即整体乘p等等)需要涉及到除，而 mod 只对加减乘有分配律。我们可以用分治加递归实现。

设 $sum(p,c) = 1 + p + p^2 + ... + p^c$，我们需要求 $sum(p,c)$。

若 c 为奇数：

$$sum(p,c) = (1 + p + ... + p^{(c - 1) / 2}) + (p^{(c + 1)/2} + ... + p^c)$$
$$=(1 + p + ... + p^{(c - 1) / 2}) + p^{(c + 1) / 2} * (1 + p + ... + p^{(c - 1) / 2})$$
$$=(1 + p^{(c + 1) / 2} * sum(p, (c / 2) - 1)$$

类似地，若 c 为偶数：

$$sum(p,c) = (1 + p^{c / 2}) * sum(p,(c / 2)-1) + p^c$$

每次分治后，问题规模会缩小一半，配合快速幂，即可在 O(log c) 的时间内求出等比数列的和。

CODE：
``` c++
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
using namespace std;

typedef long long ll;
const int MOD = 9901;
ll ans = 1, a, b, x;

ll quick_pow(ll b, ll p) {
    ll res = 1;
    for (; p; p >>= 1) {
        if (p & 1) res = res * b % MOD;
        b = b * b % MOD;
    } return res;
}

ll sum(ll p, ll c) {
    if (c == 0) return 1;
    if (c == 1) return p + 1;
    if (c & 1) {
        return (1 + quick_pow(p, (c + 1) / 2)) * sum(p, (c - 1) / 2) % MOD;
    }
    else {
        return (((1 + quick_pow(p, c / 2)) * sum(p, (c / 2) - 1)) % MOD + quick_pow(p, c)) % MOD;
    }
}

int main() {
    while (~scanf("%lld%lld", &a, &b)) {
        ans = 1;
        for (int i = 2; i <= sqrt(a); i++) {
            if (a % i == 0) {
                int tmp = 0;
                while (a % i == 0) a /= i, tmp++;
                x = tmp * b;
                (ans *= sum(i, x)) %= MOD;
            }
        }
        if (a != 1) {
            (ans *= sum(a, b)) %= MOD;
        }
        printf("%lld\n", ans);
    }
    return 0;
}
```