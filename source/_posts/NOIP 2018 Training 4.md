---
title: 'NOIP 2018 Training 4'
date: 2018-08-25 14:50:40
tags: 
    - 比赛
mathjax: true
---

### A. 字符串排位
-----

题意：给出一个长度为 n，由小写英文字母组成的字符串 S，求在所有由小写英文字母组成且长度为 n，且恰好有 k 位与 S 不同的字符串中，给定的字符串 T 按照字典序排在第几位。答案对 1e9 + 7 取模输出。

题意其实是要求比 T 字典序小且与 S 有 k 位不同的字符串个数加 1。应该算是计数类的，组合数学出场～～

每一位 i 有两种情况：等于 T[i] 或小于 T[i], 如果第 i 位就小于 T[i] 了，那么 i + 1 ~ n 位就可以任选，即 $25^{k - 1} * C_{n - i}^{k - 1}$.

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
const int mod = 1e9 + 7, N = 1e5 + 5;

char s[N], t[N];
ll n, k, inv[N], Fac[N], ans = 0;

ll quick_pow(ll b, ll p) {
    ll ret = 1;
    for (; p; p >>= 1) {
        if (p & 1) ret = ret * b % mod;
        b = b * b % mod;
    } return ret;
}

ll C(ll n, ll m) {
    if (m < n) return 0;
    return quick_pow(25, n) * Fac[m] % mod * inv[n] % mod * inv[m - n] % mod;
}

int main() {
    ios::sync_with_stdio(false);
    cin >> n >> k;
    cin >> (s + 1);
    cin >> (t + 1);
    Fac[0] = 1;
    for (int i = 1; i <= n; i++) Fac[i] = (Fac[i - 1] * i) % mod;
    for (int i = 0; i <= n; i++) inv[i] = quick_pow(Fac[i], mod - 2);
    for (int i = 1; i <= n; i++) {
        if (s[i] < t[i]) {
            ans = (ans + (t[i] - 'a' - 1) * C(k - 1, n - i) % mod + C(k, n - i)) % mod;
            k--;
        } else {
            ans = (ans + (t[i] - 'a') * C(k - 1, n - i) % mod) % mod;
            if (s[i] > t[i]) k--;
        }
        if (!k) break;
    }
    cout << (ans + 1) << endl;
    return 0;
}
```


### B. 棒棒糖
-----

题意：小 A 把一份询问两点间的LCA问题的正确代码改了改，其中被改动的部分是在预处理 dep[] 时, $dep[v] = dep[u] + Random(0, 1), v \in Son(u)$，其中 Random(0, 1) 表示在集合 {0, 1} 中等概率随机一个元素的函数。

小 A 想知道，对于题中的每一组询问，他分别有多少的概率输出的是正确的答案。若答案是一个分数，请将答案对 998244353 取模输出。

* 分数取模：对于一个有理数 $\frac{a}{b}$, 其中 $a \geq 0, b > 0$, 且 a, b 互质。对于一个质数 p，如果 b 不是 p 的倍数，我们可以定义 $\frac{a}{b}\ mod\ p$ 为使 $bx \equiv a(mod\ p)$ 的最小非负整数 x，否则 $\frac{a}{b}\ mod\ p$ 未定义。

本题保证结果有定义。