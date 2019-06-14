---
title: 'NOIP 2018 Training 4'
date: 2018-08-27 14:50:40
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


设 C LCA(u, v)。可以发现，只有 dist(u, C) = dist(v, C) 时答案是对的，于是枚举 i 表示 u 到 C 有多少个 0 边。

设 A = max(dist(u, C), dist(v, C))，此处的 dist 是原来的，全 1 的。B = min(dist(u, C), dist(v, C))。

$$\sum\limits_{i = 0}^A \binom{A}{i} \binom{B}{A - i}$$

根据范德蒙恒等式 $C_{m + n}^k = \sum\limits_{i = 0}^k C_m^iC_n^{k - i}$ 得出，上式等于 $C_{A + B}^A$.

得出的方案数不要忘了除以分母 $2^{A + B}$，最后可以用扩欧求解同余方程。

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
const int N = 2e5 + 10, mod = 998244353;
ll n, m, A, B, Fac[N], dep[N], inv[N];
int to[N << 1], nxt[N << 1], lnk[N], cnt, f[N][20];

void add(int u, int v) {
    to[++cnt] = v, nxt[cnt] = lnk[u], lnk[u] = cnt;
}

void dfs(int x, int fa) {
    for (int i = lnk[x]; i; i = nxt[i]) {
        if (to[i] != fa) {
            f[to[i]][0] = x;
            dep[to[i]] = dep[x] + 1;
            dfs(to[i], x);
        }
    }
}

void ycl() {
    for (int j = 1; j <= log2(n); j++)
        for (int i = 1; i <= n; i++)
            f[i][j] = f[f[i][j - 1]][j - 1];
}

ll lca(ll x, ll y) {
    if (dep[x] < dep[y]) swap(x, y);
    ll delta = dep[x] - dep[y];
    for (int i = 0; i <= 18; i++)
        if (delta & (1 << i)) x = f[x][i];
    if (x == y) return x;
    for (int i = 18; i >= 0; i--)
        if (f[x][i] != f[y][i])
            x = f[x][i], y = f[y][i];
    return f[x][0];
}

ll iabs(ll a) {return a > 0 ? a : -a;}

ll quick_pow(ll b, ll p) {
    ll ret = 1;
    for (; p; p >>= 1) {
        if (p & 1) ret = ret * b % mod;
        b = b * b % mod;
    } return ret;
}

ll C(ll x, ll y) {
    if (x < y) return 0;
    return Fac[x] * inv[y] % mod * inv[x - y] % mod;
}

ll exGcd(ll a, ll b, ll &x, ll &y) {
    if (b == 0) {
        x = 1, y = 0;
        return a;
    }
    ll d = exGcd(b, a % b, y, x);
    y -= a / b * x;
    return d;
}

int main() {
    ios::sync_with_stdio(false);
    cin >> n;
    for (int i = 1; i < n; i++) {
        int x, y;
        cin >> x >> y;
        add(x, y), add(y, x);
    }
    dfs(1, 0);
    ycl();
    cin >> m;
    Fac[0] = inv[0] = 1;
    for (int i = 1; i <= n; i++) {
        Fac[i] = Fac[i - 1] * i % mod;
        inv[i] = quick_pow(Fac[i], mod - 2);
    }
    for (int i = 1; i <= m; i++) {
        int u, v;
        cin >> u >> v;
        int uv = lca(u, v);
        A = dep[u] - dep[uv];
        B = dep[v] - dep[uv];
        if (A < B) swap(A, B);
        ll b = quick_pow(2, A + B);
        ll a = C(A + B, A), real_ans, y;
        exGcd(b, mod, real_ans, y);
        real_ans *= a;
        printf("%lld\n", (real_ans % mod + mod) % mod);
    }
    return 0;
}
```