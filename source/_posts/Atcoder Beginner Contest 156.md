---
title: 'Atcoder Beginner Contest 156'
date: 2020-03-01 20:30:40
tags: 
    - 比赛
mathjax: true
---

ABC自动水过...

### D
-----

code :
``` c++
/*
所以才知道单算 C(n, m) 是 O(m) 复杂度的？？？
*/
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int mod = 1e9 + 7, N = 2e5 + 10;
typedef long long ll;
ll n, a, b, fac[N];

ll quick_pow(ll a, ll b) {
    ll ret = 1;
    for (; b; b >>= 1) {
        if (b & 1) ret = ret * a % mod;
        a = a * a % mod;
    } return ret;
}

ll C(ll n, ll m) {
    if(n < m) return 0;
    ll res = 1;
    rep(i, 1, m) {
        ll a = (n + i - m) % mod;
        ll b = i % mod;
        res = res * (a * quick_pow(b, mod - 2) % mod) % mod;
    }
    return res;
}

int main() {
    cin >> n >> a >> b;
    ll ans = (quick_pow(2, n) - 1 + mod) % mod;
    ans = (ans - C(n, a) + mod) % mod;
    ans = (ans - C(n, b) + mod) % mod;
    printf("%lld\n", ans);
    return 0;
}
```

### E
-----

首先 k >= n 的时候答案是 C(2 * n - 1, n - 1)

然后 k < n 的时候就可以枚举 位置空着的人数 了！就好了！我也好奇我当时在想啥呀

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 4e5 + 10, mod = 1e9 + 7;
typedef long long ll;
ll n, K, fac[N], inv[N];

ll quick_pow(ll a, ll b) {
    ll ret = 1;
    for (; b; b >>= 1) {
        if (b & 1) ret = ret * a % mod;
        a = a * a % mod;
    } return ret;
}

ll C(ll n, ll m) {
    return fac[n] * inv[m] % mod * inv[n - m] % mod;
}

int main() {
    cin >> n >> K;

    fac[0] = inv[0] = 1;
    rep(i, 1, 2 * n) fac[i] = fac[i - 1] * i % mod;
    inv[2 * n] = quick_pow(fac[2 * n], mod - 2);
    for (int i = 2 * n; i >= 1; i--) inv[i - 1] = inv[i] * i % mod;

    if (K >= n) {
        printf("%lld\n", C(2 * n - 1, n - 1));
    } else {
        ll tot = 0;
        rep(k, 0, K)
            tot = (tot + C(n, k) * C(n - (n - k) + (n - k - 1), n - k - 1) % mod) % mod;
        printf("%lld\n", tot);
    }
    return 0;
}
```

### F
-----

这题挺好