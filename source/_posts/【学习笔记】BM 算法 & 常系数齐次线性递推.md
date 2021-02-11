---
title: '【学习笔记】BM 算法 & 常系数齐次线性递推'
date: 2020-12-13 18:49:40
tags: 
    - 学习笔记
    - 线性递推
mathjax: true
---

# $Berlekamp-Massey$

[大佬1](https://blog.csdn.net/hzj1054689699/article/details/90047110)  [大佬2](https://www.luogu.com.cn/blog/_post/234046)

实现时下标经常多一位或少一位-_-|| 我预言不久的将来 BM 会沦为 xml 的脑内黑箱

# 常系数齐次线性递推

求出递推式，接下来就要递推啦。

是照着 [zhouzhendong](https://www.cnblogs.com/zhouzhendong/p/Cayley-Hamilton.html) 和 [Winniechen](https://www.cnblogs.com/Winniechen/p/10246295.html) 的博客学习的。也可以参考 [OI-wiki](https://oi-wiki.org/math/linear-recurrence/)，是两种解释，没有那么多线代前置芝士。

其实我讲不清楚，建议点 OI-wiki，它特别清楚。

## $问题引入$

给定一个数列，满足:
* $n \leq k$ 时，$f_n = a_n$
* $n > k$ 时，$f_n = \sum\limits_{i = 1}^k f_{n - i} * a_i$

众所周知矩阵生来就做这事儿的，$O(k^3\ log\ n)$. 当 $n$ 比较大的时候就没前途。

于是就出现了——特征多项式优化矩阵快速幂。求第 $n$ 项复杂度为 $O(k^2 log n)$

## $前置数学芝士$

1. 矩阵的特征值和特征向量
    * 若对于 $n$ 阶矩阵 $A$，有常数 $\lambda$、非零列向量 $\vec{v}$ 满足 $\lambda \vec{v} = A \vec{v}$，则称 $\lambda$ 为矩阵 $A$ 的**特征值**，$\vec{v}$ 为矩阵 $A$ 的**特征向量**。

    * 上柿可以写作 $(\lambda I - A)\vec{v} = 0$, 左边括号里那玩意是矩阵的行列式，记做 $f(\lambda)$，即为矩阵 $A$ 的**特征多项式**，则对于某个矩阵的特征值 $\lambda_0$, 有 $f(\lambda_0) = 0$。

    * （注意：行列式和矩阵乘以一个数 $k$ 时是不同的，行列式是某行或某列乘 $k$，矩阵是所有数乘 $k$。别问为啥，这就是定义。）

2. 矩阵特征多项式和一个著名定理
    * 凯莱-哈密顿定理：$f(A) = 0$.

    * 求解：$f(\lambda) = |\lambda I - A| = \lambda^m - \sum\limits_{i = 0}^{k - 1} a_{k - i} \lambda^i$

由上面那个柿子得到 $f(A) = A^k - b_1A^{k - 1} - c_2A^{k - 2}  ... - c_k = 0$, 即 $A^k = c_1A^{k - 1} + ... + c_k$，这告诉我们 $A_k$ 可以被 $A^0$ ~ $A^{k - 1}$ 的矩阵线性表出！（哇，有线代内味了

$A^{x + y} = A^x * A^y$,

$A^x = \sum b_i A^i$, $A^y = \sum c_i A^i$

因为有 $f(A) = 0$，所以 $A^{x + y} = \sum\limits_{k = 0}^{2K - 2} (\sum\limits_{i = 0}^{K - 1} b_ic_{k - i})A^k\ mod\ f(A)$，这个取模达到了降次的效果。用快速幂就是 $O(K^2 logn)$ 的。

zhouzhendong 大佬博客里有~~非人~~神仙 $O(klogklogn)$ 多项式操作，然而常人都写不动吧 QAQ？（就先不学了

$Template$：
``` c++
/*
luogu_5497
加了常系数齐次线性递推的版本
*/
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
#define pb push_back
using namespace std;

typedef long long ll;
const int mod = 998244353, N = 2e4 + 10;
ll n, m, de, K;
ll a[N], b[N];
vector<ll> f[N], ans, lst;

ll add(ll x, ll y) { return (x + y) % mod; }
ll mul(ll x, ll y) { return x * y % mod; }
ll sub(ll x, ll y) { return (x - y + mod) % mod; }
void Add(ll &x, ll y) { x = (x + y) % mod; }
void Mul(ll &x, ll y) { x = x * y % mod; }
void Sub(ll &x, ll y) { x = (x - y + mod) % mod; }

ll qpow(ll a, ll b) {
    a %= mod;
    ll ret = 1;
    for (; b; b >>= 1, Mul(a, a)) if (b & 1) Mul(ret, a);
    return ret;
}

void BM(ll n) {
    int fail = 0;
    ll cur = 0;
    rep(i, 1, n) {
        cur = 0;
        for (int j = 0; j < ans.size(); j++)
            Add(cur, mul(b[i - 1 - j], ans[j]));
        if (!((b[i] - cur) % mod)) continue;
        if (!fail) {
            fail = i;
            de = b[i] - cur;
            rep(j, 1, i) ans.pb(0);
            continue;
        }
        vector<ll> now = ans;
        ll w = mul(b[i] - cur, qpow(de, mod - 2));
        if (ans.size() < lst.size() + i - fail)
            ans.resize(lst.size() + i - fail);
        Add(ans[i - fail - 1], w);
        for (int j = 0; j < lst.size(); j++)
            Sub(ans[i - fail + j], mul(w, lst[j]));
        if (now.size() < lst.size() + i - fail) {
            lst = now, fail = i, de = b[i] - cur;
        }
    }
}

void doMul(ll x[], ll y[]) {
    ll tmp[N];
    memset(tmp, 0, sizeof(tmp));
    rep(i, 0, K - 1)
        rep(j, 0, K - 1)
            Add(tmp[i + j], mul(x[i], y[j]));
    for (int i = 2 * K - 2; i >= K; i--) {  // 在取模
        if (!tmp[i]) continue;
        rep(j, 1, K)
            Add(tmp[i - j], mul(a[j], tmp[i]));
    }
    rep(i, 0, 2 * K - 1)
        x[i] = tmp[i];
}

ll c[N];  // 系数
void fpow(ll n) {
    static ll tmp[N];
    memset(tmp, 0, sizeof(tmp));
    memset(c, 0, sizeof(c));
    c[0] = tmp[1] = 1;
    for (; n; n >>= 1) {
        if (n & 1) doMul(c, tmp);
        doMul(tmp, tmp);
    }
}

int main() {
    cin >> n >> m;
    rep(i, 1, n) {
        scanf("%lld", &b[i]);
    }
    BM(n);
    K = (int)ans.size();
    rep(i, 0, K - 1) {
        ans[i] = (ans[i] % mod + mod) % mod;
        printf("%lld ", ans[i]);
        a[i + 1] = ans[i];
    }
    puts("");
    rep(i, 0, K - 1) b[i] = b[i + 1];
    rep(i, K, n) b[i] = 0;
    fpow(m);
    ll ans = 0;
    rep(i, 0, K - 1)
        Add(ans, mul(b[i], c[i]));
    printf("%lld\n", (ans % mod + mod) % mod);
    return 0;
}
```

### [$THUSCH2017-如果奇迹有颜色$](https://loj.ac/p/2981)

这题比洛谷上的 Polya 模板题多了个相邻 $m$ 项颜色不能全不同的限制。

$|X/G| = \frac{1}{|G|} \sum_g m^{c(g)} = \frac{1}{|G|} \sum_k \phi(n / k) * f(k)$

现在要算 $f(gcd = k)$ 的染色方案数。可以状压计算，状态数是 $m^{m}$，直接跑矩阵快速幂能拿到 $55$ 分的好成绩，然后我就不会了

题解告诉我要去学常系数齐次线性递推，我就滚去学了。厚厚，新知识！（于是就有了这篇拼凑起来的笔记

猜测递推式长度小于 $m^{m}$，借助超能力（打表）发现 $m = 7$ 就 $409$.

[$Code$](https://loj.ac/s/1012066)

### [$NOI2017-泳池$](https://uoj.ac/problem/316)

**恰好**用差分变为**小于等于**。设计 dp：$dp_{i, j}$ 表示有一块宽度为 $j$ 的区域，最下面 $i$ 行都是安全的，从下往上第 $i + 1$ 行及以上部分任意，**且该区域内最大游泳场面积 $\leq K$** 的概率。那么有：

$$dp_{i, j} = dp_{i + 1, j} p^j + \sum\limits_{k = 1}^j dp_{i + 1, k - 1}dp_{i, j - k}(1 - p)p^{k - 1}$$

答案是 $dp_{0, n}$。

如果你打了表。。你就会发现 $dp_0$ 有递推式（谁这么熟练啊= =）于是一顿操作猛如虎，回头一看二百五。

[$Code$](https://uoj.ac/submission/452703)