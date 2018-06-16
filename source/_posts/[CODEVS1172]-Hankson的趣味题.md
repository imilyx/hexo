---
title: '[CODEVS1172]-Hankson的趣味题'
date: 2018-06-16 14:50:40
tags: 
    - 最大公约数与最小公倍数
mathjax: true
---

http://codevs.cn/problem/1172/

题意：有 n 个询问，每个询问中有四个自然数 a, b, c, d, 然后求有多少个 x 满足 $gcd(a, x) = c$ 且 $lcm(b, x) = d$ 。
$n \leq 2000$ , $1 \leq a, b, c, d \leq 2 * 10^9$ 。

解法一：

d 的约数个数上界是 $\sqrt{d}$ ，但 1 ~ d 中每个数约数个数大概只有 $log d$ ，所以我们可以预处理出 1～$\sqrt{2 * 10^5}$ 的所有质数，然后用搜索算法组成 d 的所有约数，再判断是否满足题目条件，即可得到100分。
——lyd

而我写的是这个：

解法二：

因为 x 是 d 的约数，所以 x 的质因子 p 一定也是 d 的质因子。我们可以对 d 的每个质因子 p 分别计算 x 可能包含多少个 p 。

设 a, b, c, d, x 分别包含 $m_a$ , $m_b$ , $m_c$ , $m_d$ , $m_x$ 个质因子 p ，其中 $m_x$ 是未知量。

如果 $m_a == m_c$ 且 $m_b == m_d$ 且 $m_c \leq m_d$ 那么 $m_x$ 只要满足 $m_c \leq m_x \leq m_d$ 就可以了，方案数为 $m_d - m_c + 1$；
若 $m_c > m_d$ 那本次询问答案无解，为0。

若 $m_a \neq m_c$ 且 $m_b \neq m_d$ ，那么 $m_x$ 必须等于 $m_c$ 和 $m_d$ ，若 $m_c \neq m_d$ 则无解。

我们把 $m_x$ 的取法数记为 $cnt_p$ ，也就是 x 包含质因子 p 的方案有 $cnt_p$ 种。根据乘法原理，满足题意的 x 数量即为连乘积：

$$\prod_{p | d} cnt_p$$

同样我们预处理出 1～$\sqrt{2 * 10^5}$ 的所有质数，因为 d 至多包含 10 个质因子，所以计算量很小，深搜解决，时间复杂度 $O(n\sqrt{d} / logd)$ 。

CODE：
``` c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
const int N = 50005;
ll n, a0, a1, b0, b1, tot, ans;
int prime[N];
bool mark[N];

void Euler_prime() {
    for (int i = 2; i <= N; i++) {
        if (!mark[i]) prime[++tot] = i;
        for (int j = 1; j <= tot && prime[j] * i <= N; j++) {
            mark[prime[j] * i] = 1;
            if (i % prime[j] == 0) break;
        }
    }
}

void solve(int x) {
    int m0, m1, m2, m3;
    m0 = m1 = m2 = m3 = 0;
    while (a0 % x == 0) {a0 /= x, m0++;}
    while (a1 % x == 0) {a1 /= x, m1++;}
    while (b0 % x == 0) {b0 /= x, m2++;}
    while (b1 % x == 0) {b1 /= x, m3++;}
    if (m0 == m1 && m2 == m3) {
        if (m1 <= m3) ans *= (m3 - m1 + 1);
        else ans = 0;
    }
    else if (m0 != m1 && m2 != m3 && m1 != m3) ans = 0;
}

int main() {
    Euler_prime();
    scanf("%lld", &n);
    while (n--) {
        ans = 1;
        scanf("%lld%lld%lld%lld", &a0, &a1, &b0, &b1);
        for (int i = 1; i <= tot; i++) solve(prime[i]);
        if (b1 != 1) solve(b1);
        printf("%lld\n", ans);
    }
    return 0;
}
```