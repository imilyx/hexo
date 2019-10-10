---
title: 'Atcoder Grand Contest 038'
date: 2019-10-09 11:50:40
tags: 
    - 比赛
mathjax: true
---

### A - 01 Matrix
-----

想了一会儿。。qwq

显然，令 mp[1 ~ b, 1 ~ a] = 1, mp[b + 1 ~ n, a + 1 ~ m] = 1 即可。

### B - Sorting a Segment
-----

我们发现，若排序 (l1, r1) 得到的序列与排序 (l2, r2) 得到的序列相同(r1 < l2)，那么排序 (l1, r1), (l1 + 1, r1 + 1), (l1 + 2, r1 + 2) ... (l2 - 1, r2 - 1), (l2, r2) 得到的序列也是相同的。

排序 (l, r) 与 (l + 1, r + 1) 得到的序列相同当且仅当在 [l, r + 1] 中，a[l] 是最小值，a[r + 1] 是最大值。这个用单调队列判断即可，最后用方案总数减去相同的方案数得出答案！

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 2e5 + 10;
int n, K, a[N], q[N], ans, mark[N], b[N];

int main() {
    cin >> n >> K;
    rep(i, 1, n) cin >> a[i];
    ans = n - K + 1;

    int cnt = 0, tot = 0;
    rep(i, 1, n) {
        if (a[i] > a[i - 1]) b[i] = b[i - 1] + 1;
        else b[i] = 1;
    }

    int l = 1, r = 0;
    rep(i, 1, n) {
        if (i >= K) {
            while (l <= r && q[l] < i - K) ++l;
        }
        if (q[l] == i - K) mark[i]++;
        while (l <= r && a[q[r]] > a[i]) --r;
        q[++r] = i;
    }

    l = 1, r = 0;
    for (int i = n; i >= 1; i--) {
        if (i <= n - K + 1) {
            while (l <= r && q[l] > i + K) ++l;
        }
        if (q[l] == i + K) mark[i + K]++;
        while (l <= r && a[q[r]] < a[i]) --r;
        q[++r] = i;
    }

    bool ff = 0;
    rep(i, K, n) {
        if (b[i] >= K) {
            if (!ff) ff = 1;
            else tot++;
        } else if (mark[i] == 2) tot++;
    }
    printf("%d\n", ans - tot);
    return 0;
}
```

### C - LCMs
-----

数论题！Atcoder真全

$\sum\limits_{i=1}^{n}\sum\limits_{j=i + 1}^n\frac{A_iA_j}{gcd(A_i,A_j)}$

= $\sum\limits_{d=1}^N\frac{1}{d}\sum\limits_{i=1}^n\sum\limits_{j=i+1}^nA_iA_j\sum\limits_{kd|A_i,kd|A_j}\mu(k)$

= $\sum\limits_{d=1}^N\frac{1}{d}\sum\limits_{k=1}^{N/d}\mu(k)(\sum\limits_{kd|A_i}\sum\limits_{kd|A_j}A_iA_j)$

右边这个 “()” 里的部分可以调和级数复杂度预处理

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (long long i = x; i <= y; i++)
using namespace std;

const int N = 1e6, mod = 998244353, M = 2e5 + 10;
typedef long long ll;
ll n, a[M], inv[N + 5], bin[N + 5], ans, f[N + 5];
int mu[N + 5], prime[N + 5], tot, mark[N + 5];

void prework() {
    mu[1] = 1;
    rep(i, 2, N) {
        if (!mark[i]) prime[++tot] = i, mu[i] = -1;
        for (int j = 1; j <= tot && i * prime[j] <= N; j++) {
            mark[i * prime[j]] = 1;
            if (i % prime[j] == 0) {
                mu[i * prime[j]] = 0; break;
            }
            mu[i * prime[j]] = -mu[i];
        }
    }
    inv[1] = 1;
    rep(i, 2, N) inv[i] = inv[mod % i] * (mod - mod / i) % mod;
}

int main() {
    prework();
    cin >> n;
    rep(i, 1, n) cin >> a[i], bin[a[i]]++;  // 小技巧
    rep(i, 1, N) {
        ll sum = 0;
        for (ll j = i; j <= N; j += i) {
            sum = (sum + bin[j] * j % mod) % mod;
            f[i] = (f[i] + j * j * bin[j] % mod) % mod;
        }
        f[i] = (sum * sum % mod - f[i] + mod) * inv[2] % mod;
    }
    rep(k, 1, N)
        for (int i = k; i <= N; i += k) // i = k * d
            ans = (ans + inv[k] * mu[i / k] % mod * f[i] % mod) % mod;
    printf("%lld\n", ans);
    return 0;
}
```

### 