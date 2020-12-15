---
title: 'Jcvb《组合计数问题》题解'
date: 2020-07-30 18:25:40
tags:
    - 容斥原理
    - 计数
mathjax: true
---

* 组合数、stirling数
* 生成函数 + FFT/NTT
* 一些特定的结论（矩阵树，burnside，prufer...）
* 一些思想（容斥、补集转化）

都是常用的技巧！

DP 每道都是吧 qvq 太重要了，有时用 FFT/NTT 加速。

## [$[TC12004]-SetAndSet$](https://vjudge.net/problem/Topcoder-12004)
-----

按位考虑。

若对于某一位，存在一个数当前位为 0，则这一位为 0 的数不能全在一边。

不能全在一边 就很难做，考虑**补集转化**，计算 全在一边 的。我们发现这样会算重，准确来说算的是“至少有几位不同”。

考虑**容斥思想**，答案 = 总数 - 至少 1 位不同 + 至少 2 位不同 - ...

用 dfs 枚举，并查集维护即可，将枚举位为 0 的并到一块儿去。

## $[BZOJ1471]-不相交路径$
-----

不相交难做，相交好做。考虑**容斥**第一个相交的点。计算总方案数的时候用到了拓扑序（路径上排在 x 前面的点 y，拓扑序一定比 x 小）

## $[SHOI2009]-舞会$
-----

就是那道-已经没有什么好害怕的了，dp 计算 g 数组，f[i, j] 表示前 i 个女生有 j 个匹配了。

## $[某CEOI题]$
-----

什么容斥小于号改为大于号的，咕咕

## $[TC14170]-Divfree$
-----

想不到的容斥dp。。

不合法的状态 (A, B) 是 B 是 A 的真约数时。数字 k 的真约数只有 log(k) 个，k 是 50000，也就是说不合法的链的长度最多 16。

考虑容斥，f[i] 表示**合法的**长度为 i 的链方案数，g[i] 表示**不合法的**长度为 i 的链方案数。g 可以预处理。

$f[n] = f[n - 1] * g[1] - f[n - 2] * g[2] + f[n - 3] * g[3]...$ 太妙了。

## $[稳定多米诺覆盖]$
-----

[链接](https://imilyx.github.io/2020/07/30/[51nod1518]-%E7%A8%B3%E5%AE%9A%E5%A4%9A%E7%B1%B3%E8%AF%BA%E8%A6%86%E7%9B%96/)

## $[TC13891]-Gxor$
-----

一道斯特林数相关的数学题

f[i] 表示至少 i 个连通块，g[i] 表示恰好 i 个连通块，我们要求 g[1]

考虑一个实际连通块数量为 p 的图，它会在我们枚举 k 个连通块的时候被算 $S_2(p, k)$ 次

有个柿子：$\sum_i (-1)^{i - 1} S_2(n, i) * (i - 1)! = [n = 1]$

[比较清楚的证明](https://blog.csdn.net/qq_35950004/article/details/106126524)

好神奇啊，消化消化。。。

$g[1] = \sum\limits_{i = 1}^n f[i] * S_1(i, 1) * (-1)^{i - 1}$

f[k] 很难算的样子，怎么办？注意到 n 只有 9，暴搜，强制块与块之间的边不能连（这个高斯消元解异或方程组来搞）。C(17, 9) 左右，嗯很稳！

代码有一些小细节。

``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

typedef long long ll;
const int N = 500;

class Gxor {
public:
    ll T, n, ans, len, tot;
    ll fac[15], a[N], b[N], col[N], lg[1 << 11];
    char s[15][N];

    void dfs(int x, int d) {
        if (!x) {
            int t = 0;
            tot = 0;
            rep(u, 0, n - 1) {
                rep(v, u + 1, n - 1) {
                    if (col[u] != col[v]) a[tot++] = b[t];
                    ++t;
                }
            }
            a[tot] = 0;
            int cur = 0;
            rep(i, 0, T - 1) {
                if (!((a[cur] >> i) & 1)) {
                    rep(j, cur + 1, tot - 1) {
                        if ((a[j] >> i) & 1) {
                            swap(a[j], a[cur]); break;
                        }
                    }
                }
                rep(j, cur + 1, tot - 1) {
                    if ((a[j] >> i) & 1) {
                        // swap(a[j], a[cur]);
                        a[j] ^= a[cur];
                    }
                }
                if ((a[cur] >> i) & 1) ++cur;
            }
            ans += fac[d] * (1ll << (T - cur));
            return;
        }
        for (int t = x; t; t = (t - 1) & x)
            if (t & (x & -x)) {  // 包含 x 的末尾 1 位置，这样不会重复枚举
                for (int u = t; u; u = (u - 1) & u)
                    col[lg[u & -u]] = lg[x & -x];
                dfs(x - t, d + 1);
            }
    }

    ll countsubs(vector<string> s) {
        T = s.size();
        len = s[0].length();
        for (int i = 1; ; i++) if (i * (i - 1) / 2 == len) { n = i; break; }
        rep(i, 0, T - 1) {
            int t = 0;
            rep(u, 0, n - 1)
                rep(v, u + 1, n - 1) {
                    if (s[i][t] == '1') b[t] |= (1ll << i);
                    ++t;
                }
        }
        fac[0] = fac[1] = 1;
        rep(i, 0, 10) lg[(1 << i)] = i;
        rep(i, 2, 10) fac[i] = fac[i - 1] * (i - 1);
        for (int i = 2; i <= 10; i += 2) fac[i] = -fac[i];
        dfs((1 << n) - 1, 0);
        return ans;
    }
};
```