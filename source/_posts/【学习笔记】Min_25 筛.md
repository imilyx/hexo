---
title: '【学习笔记】Min_25 筛'
date: 2021-09-05 09:50:40
tags: 
    - 学习笔记
mathjax: true
---

$O(\frac{n^{\frac{3}{4}}}{logn})$ 求解积性函数前缀和！

感想：需全程牢记积性函数的定义，否则会闹出哭笑不得的事情 = =

## 求 $G$
---

设 $min(i)$ 表示 $i$ 的最小质因子。

设 $g(n, j) = \sum\limits_{i = 1}^n [ \text{i is a prime or min(i) > p[j] } ] f'(i)$，大概表示 $1$ ~ $n$ 里是质数或最小质因子大于第 $j$ 个质数的 $k$ 次幂之和，其中 $f'(i)$ 是一个和 $f$ 在质数处的取值完全相同的完全积性函数。以 [模板](https://www.luogu.com.cn/problem/P5325) 为例，$f'(i) = i^k$。

~~$g$ 有什么用？请看完整篇……~~

$1$ ~ $n$ 所有质数的 $k$ 次幂之和显然等于 $g(n, sp(n))$，其中 $sp(n)$ 为 $\leq \sqrt{n}$ 的最大质数。我们先将它表示为 $G(n)$，在后期求解中会用到。

考虑从 $g(n, j - 1)$ 转移到 $g(n, j)$，减掉的应当恰好是那些 $min = p_j$ 的合数的 $k$ 次幂。

$$
g(n, j) = g(n, j - 1) - p_j^k \left ( g(\frac{n}{p_j}, j - 1) - g(p_{j - 1}, j - 1) \right )
$$

这是对的因为 $g$ 是个完全积性函数，虽然提出一个 $p_j$ 之后可能还有 $p_j$ 这个因子，但 dp 值可以直接相乘，这很好的。

$g(p_{j - 1}, j - 1)$ 其实就是前 $j - 1$ 个质数的 $k$ 次方和，因为 $p_{j - 1} \leq \sqrt{n}$，这部分可以直接预处理。

## 求 $S$
---

设 $S(n, x) = \sum\limits_{i = 1}^n [\text{i is a prime or min(i) > p[x]}] f(i)$，即 $S(n, x)$ 表示 $n$ 以内所有质数，以及所有最小质因子 $> p[x]$ 的合数的贡献

$ans = \sum\limits_{i = 1}^n f(i) = S(n, 0)$

$S(n, j)$ 可以划分为两部分，第一部分为 $> p_j$ 的质数的贡献，也就是 $G(n) - \sum\limits_{i = 1}^j p_i^k$；另一部分是最小质因子 $> p_j, \leq \sqrt{n}$ 的合数的贡献，就挨个儿枚举最小质因子及其次数 就可以啦

~~为什么要枚举次数啊？你怕不是忘记积性函数是啥了 = =~~

$$
S(n, j) = G(n) - \sum\limits_{i = 1}^j p_i^k + \sum\limits_{1 \leq p_i^e \leq n, i > j} f(p_i^e) \left ( S(\frac{n}{p_i^e}, i) + [e \neq 1] \right )
$$

魔改版本也是屡见不鲜，随机应变吧

你注意到要求的 $G(\lfloor \frac{n}{\Box} \rfloor)$ 只有 $\sqrt{n}$ 种，这里就涉及到标号与值的对应关系，可以关于 $\sqrt{n}$ 分开存。

---

细节：

- “$1$” 的处理：为了方便，不将它算入 $g$ 或 $S$，只在最后特判一下。

---

### [$\text{LUOGU5325-}$模板](https://www.luogu.com.cn/problem/P5325)

<details>
  <summary>template</summary>
  ``` c++
  /*
  luogu_5325
  积性函数满足 f(p^k) = p^k(p^k - 1)
  求 \sum\limits_{i = 1}^n f(i)
  */
  #include <bits/stdc++.h>
  #define rep(i, x, y) for (int i = x; i <= y; i++)
  #define per(i, x, y) for (int i = x; i >= y; i--)
  #define vi vector<int>
  #define pb push_back
  #define mkp make_pair
  #define fi first
  #define se second
  #define gc getchar
  #define db double
  #define bs bitset
  #define ll long long
  #define lll __int128
  #define ui unsigned int
  #define ull unsigned long long
  #define pii pair<int, int>
  #define clr(x) memset(x, 0, sizeof(x))
  #define lbit(x) ((x) & (-(x)))
  #define heap priority_queue<int>
  #define SI set<int>::iterator
  #define MSI multiset<int>::iterator
  #define Go(i, x, v) for (int i = 0, x = (i == v.size() ? 0 : v[i]); i < v.size(); i++, x = (i == v.size() ? 0 : v[i]))
  using namespace std;
  template <class T> void rd(T &x) {
    x = 0; char ch = gc(); int f = 1;
    for (; !isdigit(ch); ch = gc()) if (ch == '-') f = -1;
    for (; isdigit(ch); ch = gc()) x = (x << 1) + (x << 3) + ch - '0';
    x *= f;
  }
  template <class T> void cmin(T &x, T y) { x = min(x, y); }
  template <class T> void cmax(T &x, T y) { x = max(x, y); }
  const int mod = 1e9 + 7, i3 = (mod + 1) / 3;
  template <class T> T add(T x, T y) { return x + y >= mod ? x + y - mod : x + y; }
  template <class T> T sub(T x, T y) { return x - y < 0 ? x - y + mod : x - y; }
  template <class T> T mul(T x, T y) { return 1ll * x * y % mod; }
  template <class T> void Add(T &x, T y) { x = (x + y >= mod ? x + y - mod : x + y); }
  template <class T> void Sub(T &x, T y) { x = (x - y < 0 ? x - y + mod : x - y); }
  template <class T> void Mul(T &x, T y) { x = 1ll * x * y % mod; }
  template <class T> T qpow(T a, T b = mod - 2) { T ret = 1; for (; b; b >>= 1, Mul(a, a)) if (b & 1) Mul(ret, a); return ret; }
  template <class T> void nm(T &a) { a = (a % mod + mod) % mod; }
  /* ============ Header Template ============ */
  const int N = 1e6 + 10;
  ll n, sn;
  ll g1[N], g2[N], sp1[N], sp2[N];
  ll rid[N];  // real id
  ll pri[N], mem[2][N];
  int mark[N], tot, idx;
  ll S1(ll x) { return x * (x + 1) / 2 % mod; }
  ll S2(ll x) { return x * (x + 1) / 2 % mod * (2 * x + 1) % mod * i3 % mod; }
  ll &pos(ll x) { return x <= sn ? mem[0][x] : mem[1][n / x]; }
  void euler(int n) {
    rep(i, 2, n) {
      if (!mark[i]) {
        pri[++tot] = i;
        sp1[tot] = (sp1[tot - 1] + i) % mod;
        sp2[tot] = (sp2[tot - 1] + 1ll * i * i % mod) % mod;
      }
      for (int j = 1; j <= tot && i * pri[j] <= n; j++) {
        mark[i * pri[j]] = 1;
        if (i % pri[j] == 0) break;
      }
    }
  }
  ll S(ll n, ll x) {
    if (pri[x] >= n) return 0;
    int k = pos(n);
    ll ret = ((g2[k] - g1[k] + mod) % mod - (sp2[x] - sp1[x] + mod) % mod + mod) % mod;
    for (int i = x + 1; i <= tot && 1ll * pri[i] * pri[i] <= n; i++) {
      ll p = pri[i];
      for (int e = 1; p <= n; ++e, p *= pri[i]) {
        ll tmp = p % mod;
        (ret += tmp * (tmp - 1) % mod * (S(n / p, i) + (e != 1)) % mod) %= mod;
      }
    }
    return ret % mod;
  }
  signed main() {
    rd(n);
    sn = sqrt(n);
    euler(sn);
    for (ll l = 1, r; l <= n; l = r + 1) {
      r = n / (n / l);
      rid[++idx] = n / l;
      g1[idx] = (n / l) % mod;
      g2[idx] = S2(g1[idx]) - 1;  // 减去 1
      g1[idx] = S1(g1[idx]) - 1;
      pos(n / l) = idx;
    }
    for (int i = 1; i <= tot; i++) {
      for (int j = 1; j <= idx && 1ll * pri[i] * pri[i] <= rid[j]; j++) {  // j 从小往大，实际上在从大往小枚举 n 遍历第 j 层的 g
        ll k = pos(rid[j] / pri[i]);
        g1[j] -= pri[i] * (g1[k] - sp1[i - 1] + mod) % mod;
        g2[j] -= pri[i] * pri[i] % mod * (g2[k] - sp2[i - 1] + mod) % mod;
        nm(g1[j]), nm(g2[j]);
      }
    }
    printf("%lld\n", ((S(n, 0) + 1) % mod + mod) % mod);  // 加回来 1
    return 0;
  }
  ```
</details>

### [$\text{Misaka Network}$ 与求和](https://loj.ac/p/572)

好题，可以说加深了一丢丢对 min_25 的理解。

$$
ans = \sum\limits_{d = 1}^n f(d)^k (2 \sum\limits_{i = 1}^{n / d} \varphi(i) - 1)
$$

问题在于如何处理 $f^k$ 前缀和。

一个质数 $d$ 的贡献是 $d^k \times [d$ 作为次大质因子的次数$]$。

min_25 的本质是质因数分解。而所有对答案有贡献的 $d$ 一定不超过 $\sqrt{n}$，也就是说一定会被筛到/分解掉。这题统计的仅是合数贡献。预处理质数个数，在筛/分解每个质数的时候算一下其作为次大质因子的贡献即可。

复杂度显然也是 $O(\frac{n^{3}{4}}{\log n})$。

但是开 unsigned int 本地 AC 提交 WA，改 long long 就过了，白菜狗迷惑.jpg

[$Code$](https://loj.ac/s/1246490)

### [$\text{UOJ188-Sanrd}$](https://uoj.ac/problem/188)

misaka network 严格弱弱弱弱化版。注意这里 $1$ 不产生贡献。

[$Code$](https://uoj.ac/submission/506685)

### [$\text{51nod1847-}$奇怪的数学题](https://www.51nod.com/Challenge/Problem.html#problemId=1847)

套路的反演一波，问题转为求 $\sum\limits_{i = 1}^n f(i)^k$，其中 $f(i) = \frac{i}{min(i)}$

观察一波，发现在求 $g$ 时减掉的「最小质因子为 $p$ 的数除以 $p$ 的值」的 $k$ 次方和就是这题我们需要的东西。顺便记录就可以了！

~~废物利用知道吗！！！~~

以及这题 $1$ 是有贡献的，所以还要数质数个数。

更烦的是 $k$ 不是 $0$、$1$、$2$ 这样的清真玩意，初始化时还要自然数幂！！！

upd: 模 $2^{32}$，组合数都不好写，垃圾出题人 /no

[$Code$](https://www.51nod.com/Challenge/ProblemSubmitDetail.html#judgeId=1204429)

### [$\text{LOJ6053-}$简单的函数](https://loj.ac/p/6053)

崭新的一天，从 min25 板子题开始！（bushi

质数 $f(p) = p - 1$，$p^c$ 的形式在 min_25 考虑合数贡献的时候改一下贡献计算方式就可以了。注意考虑 $2$ 的贡献。

[$Code$](https://loj.ac/s/1247130)

### [集训队作业$2018-$人类的本质](https://uoj.ac/problem/448)

单写。

### [整点计数](https://loj.ac/p/3069)

咕咕咕