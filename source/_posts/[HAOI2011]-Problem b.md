---
title: '[HAOI2011]-Problem b'
date: 2019-07-23 20:25:40
tags: 
    - 莫比乌斯
mathjax: true
---

[传送门](https://www.lydsy.com/JudgeOnline/problem.php?id=2301)

套路题啊，推一发【强撑

就是 [Zap](https://www.lydsy.com/JudgeOnline/problem.php?id=1101) 的加强版不是吗。。

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 5e4 + 10;
typedef long long ll;
int T, a, b, c, d, K;
int pri[N], cnt, mark[N], mu[N];
ll f[N];

void prework() {
    mu[1] = 1;
    rep(i, 2, 5e4) {
        if (!mark[i]) pri[++cnt] = i, mu[i] = -1;
        rep(j, 1, cnt) {
            if (pri[j] * i > 5e4) break;
            mark[pri[j] * i] = 1;
            if (i % pri[j] == 0) { mu[i * pri[j]] = 0; break; }
            mu[i * pri[j]] = -mu[i];
        }
    }
    rep(i, 1, 5e4) f[i] = f[i - 1] + mu[i];
}

ll calc(int n, int m) {
    if (n > m) swap(n, m);
    ll ret = 0;
    for (int i = 1, j; i <= n; i = j + 1) {
        j = min(n / (n / i), m / (m / i));
        ret += (f[j] - f[i - 1]) * (n / i) * (m / i);
    } return ret;
}

int main() {
    prework();
    cin >> T;
    while (T--) {
        cin >> a >> b >> c >> d >> K;
        a--, c--;
        a /= K, b /= K, c /= K, d /= K;
        ll ans = calc(b, d) - calc(b, c) - calc(a, d) + calc(a, c);
        printf("%lld\n", ans);
    }
    return 0;
}
```