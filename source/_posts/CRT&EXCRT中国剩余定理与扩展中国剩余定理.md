---
title: 'CRT&EXCRT中国剩余定理与扩展中国剩余定理'
date: 2019-06-02 14:50:40
tags: 
    - 同余方程
mathjax: true
---

这真的超容易忘。。。所以特此记录一篇博客！

## CRT（中国剩余定理）
-----

* 前言：必须是模数两两互质的情况哦！

中国剩余定理，又称孙子定理（古人好厉害哇

是用来解决下面这种同余方程组的：

$$
\begin{cases}
x\equiv a_1(mod\ m_1)\\
x\equiv a_2(mod\ m_2)\\
\ \vdots\\
x\equiv a_k(mod\ m_k)
\end{cases}$$

可以假设 $n_1 \equiv a_1(mod\ m_1)$, $n_2 \equiv a_2(mod\ m_2)$, $\cdots$, $n_k \equiv a_k(mod\ m_k)$

设 $M = m_1 * m_2 * ... * m_k$. $M_i = M / m_i$.

为了使得 $n_1 + n_2 \equiv a_1(mod\ m_1)$, n2 必须是 m1 的倍数。同理可得：

* n1 是 m2 和 m3 的倍数（是 M1 的倍数
* n2 是 m1 和 m3 的倍数（是 M2 的倍数
* n3 是 m1 和 m2 的倍数（是 M3 的倍数

这样的话，为 n1 + n2 + n3 的解就满足上述方程啦！

设 $M_1t_1 \equiv 1(mod\ m_1)$，则 $a_1M_1t_1 \equiv a_1(mod\ m_1)$，我们可以用扩欧求出 t1. 所以 $n_1 = a_1M_1t_1$.

$x = \sum_{i = 1}^k n_i = \sum_{i = 1}^ka_iM_it_i$. 求完啦！就这样~


## EXCRT（扩展中国剩余定理）
-----

说是扩展，其实这俩玩意儿半分钱关系也木有。。

两个问题唯一的区别就是：EXCRT 模数不互质

怎么做捏？假设有 n 个方程。$x \equiv a_1(mod\ m_1)$，即 $x = m_1 * k_1 + a_1$; $x \equiv a_2(mod\ m_2)$, 即 $x = m_2 * k_2 + a_2$.

联立：$m_2 * k_2 - m_1 * k_1 = r_1 - r_2$. 其中只有 k1 和 k2 是未知的，我们可以用扩欧判断有无解，求出 k1, 求出 x。

我们把这个 x 叫做 x0. x 的通解就是 $x_0 + k * lcm(m_1, m_2)$, 即 $x \equiv x_0(mod\ lcm(m_1, m_2))$

就这样，我们把前两个方程变为一个方程了。

用这样的方法做 n - 1 次扩欧，就可以求出最终的 x 了。

code :
``` c++
#include <cstdio>
#include <iostream>
using namespace std;

typedef long long ll;
const int N = 1e5 + 10;
int n;
ll m[N], r[N];

ll exGCD(ll a, ll b, ll &x, ll &y) {
    if (!b) {
        x = 1, y = 0; return a;
    }
    ll d = exGCD(b, a % b, y, x);
    y -= (a / b) * x;
    return d;
}

ll exCRT() {
    ll M = m[1], R = r[1], x, y, d;
    for (int i = 2; i <= n; i++) {
        d = exGCD(m[i], M, y, x);
        if ((R - r[i]) % d) return -1;
        x = (R - r[i]) / d * x % m[i];
        R -= M * x;
        M = M / d * m[i];
        R %= M;
    }
    return (R % M + M) % M;
}

int main() {
    scanf("%lld", &n);
    for (int i = 1; i <= n; i++) scanf("%lld%lld", &m[i], &r[i]);
    printf("%lld\n", exCRT());
    return 0;
}
```