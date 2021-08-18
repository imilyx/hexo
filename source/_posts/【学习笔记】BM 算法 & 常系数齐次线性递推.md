---
title: '【学习笔记】BM 算法 & 常系数齐次线性递推'
date: 2020-12-13 18:49:40
tags: 
    - 学习笔记
    - 线性递推
mathjax: true
---

## $Berlekamp-Massey$

[大佬1](https://blog.csdn.net/hzj1054689699/article/details/90047110)  [大佬2](https://www.luogu.com.cn/blog/_post/234046) [大佬3](https://blog.csdn.net/qq_42101694/article/details/103811029)

实现时下标经常多一位或少一位-_-|| 我预 言不久的将来 BM 会沦为 xml 的脑内黑箱

大概做题思路就是：矩阵乘法都有递推式，数据范围超过 1000 就考虑 BM 求递推式然后乱搞

## 常系数齐次线性递推

求出递推式，接下来就要递推啦。

是照着 [zhouzhendong](https://www.cnblogs.com/zhouzhendong/p/Cayley-Hamilton.html) 和 [Winniechen](https://www.cnblogs.com/Winniechen/p/10246295.html) 和 [OI-wiki](https://oi-wiki.org/math/linear-recurrence/)学习的，oi-wiki 没有那么多线代前置芝士。

### 解决什么问题？

给定一个数列，满足:
* $n \leq k$ 时，$f_n = a_n$
* $n > k$ 时，$f_n = \sum\limits_{i = 1}^k f_{n - i} * a_i$

众所周知矩阵生来就做这事儿的，但是 $n$ 比较大的时候就没前途。于是就出现了——特征多项式优化矩阵快速幂。求第 $n$ 项复杂度为 $O(k^2 log n)$

upd: 原先写的不好，删了。复读 oi-wiki。

从生成函数的角度来理解，就是将每个 $x^{n - i}$ 的系数乘上一个常数转移给了 $x^n$。

构造一个理论上存在的函数 $F$。

定义：$F(\sum a_i x^{n - i})$ 表示 $\sum a_i f_{n - i}$。

显然 $F$ 可以加、乘。$F(x^n) = f_n = F(\sum\limits_{i = 1}^k a_i x^{n - i})$，有 $F(x^n - \sum\limits_{i = 1}^k a_i x^{n - i}) = F(x^{n - k}(x^k - \sum\limits_{i = 1}^k a_i x^{k - i})) = 0$。

设 $\lambda = x^k - \sum\limits_{i = 1}^k a_i x^{k - i}$（$\lambda$ 也称为该递推式的特征多项式），显然有 $F(\lambda) = 0$

$F(A(x) + x^d \lambda) = F(A(x)) + F(x^d \lambda) = F(A(x))$（这里的 $A(x)$ 是任意多项式，如果恰好是递推式的多项式那取模就是保留前 $k$ 项啦），就可以将 $A(x)$ 对 $\lambda$ 取模来降低 $A(x)$ 的次数。所以本质是个多项式取模。$O(k^2 logn)$ 的版本比较常用。

模板：要求 $f_n$，即求 $F(x^n) = F(x^n \mod \lambda)$，求出 $x^n \mod \lambda$ 后带入 $F$ 定义即可。

可以用多项式科技做到 $O(klogklogn)$。

<details>
  <summary>template</summary>
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
</details>

### [$THUSCH2017-如果奇迹有颜色$](https://loj.ac/p/2981)

这题比洛谷上的 Polya 模板题多了个相邻 $m$ 项颜色不能全不同的限制。

$|X/G| = \frac{1}{|G|} \sum_g m^{c(g)} = \frac{1}{|G|} \sum_k \varphi(n / k) * f(k)$

现在要算 $f(gcd = k)$ 的染色方案数。可以状压计算，状态数是 $m^{m}$，直接跑矩阵快速幂能拿到 $55$ 分的好成绩，然后我就不会了

题解告诉我要去学常系数齐次线性递推，我就滚去学了。厚厚，新知识！（于是就有了这篇拼凑起来的笔记

猜测递推式长度小于 $m^{m}$，借助超能力（打表）发现 $m = 7$ 就 $409$.

[$Code$](https://loj.ac/s/1012066)

### [$NOI2017-泳池$](https://uoj.ac/problem/316)

**恰好**用差分变为**小于等于**。设计 dp：$dp_{i, j}$ 表示有一块宽度为 $j$ 的区域，最下面 $i$ 行都是安全的，从下往上第 $i + 1$ 行及以上部分任意，**且该区域内最大游泳场面积 $\leq K$** 的概率。那么有：

$$dp_{i, j} = dp_{i + 1, j} p^j + \sum\limits_{k = 1}^j dp_{i + 1, k - 1}dp_{i, j - k}(1 - p)p^{k - 1}$$

答案是 $dp_{0, n}$。

如果你打了表。。你就会发现 $dp_0$ 有递推式（谁这么熟练啊= =）于是一顿操作猛如虎~~回头一看二百五~~

[$Code$](https://uoj.ac/submission/452703)

### [$CF865G-Flowers\ and\ Chocolate$](https://www.luogu.com.cn/problem/CF865G)

不错的题，比前两道靠谱。写学习笔记里。

~~什么呀！做题还能被塞狗粮，真是够了。~~

题意好绕：买若干朵花和若干盒巧克力，使得花瓣数和巧克力块数相等。花和盒子摆放有顺序。

$n$ 这么大显然生成函数了。

- 花：$F(x) = (\sum x^{p_i})^n$
- 巧：$B(x) = \frac{1}{1 - \sum x^{c_i}}$
- $ans = \sum\limits_{n \geq 0} [x^n] F(x) [x^n] B(x)$

$B(x)$ 是个常系数递推式。可以简单的从组合意义（就是背包 dp = =）来理解：
- 设 $bin_i = \sum\limits_{j = 1}^B [c_j == i]$
- 设 $k = \max(c_i)$
- $dp_i = \sum\limits_{j = 1}^{k} bin_j dp_{i - j}$

那么求出递推式的特征多项式 $D(x) = x^k - \sum bin_i x^{k - i}$

假设知道了 $F(x)$ 某个 $[x^p]$，要知道 $B(x)$ 的 $[x^p]$ 就求出 $B(x) \mod D(x)$，得到一个 $250$ 次多项式，实际上这里取模就是保留前 $k$ 项，背包即可。

对 $F(x)$ 也求对 $D(x)$ 取模的结果以降次，具体来说是把 $x^{p_i}$ 对 $D(x)$ 取模并加在一起 $n$ 次方

[$Code$](https://codeforces.com/contest/865/submission/114985231)