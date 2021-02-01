---
title: '[BZOJ2820]-YY的GCD'
date: 2019-07-24 20:30:40
tags: 
    - 莫比乌斯
mathjax: true
hidden: true
---

[传送门](https://www.luogu.org/problemnew/show/P2257)

gcd(x, y) 为质数 d，也就是 gcd(x / d, y / d) = 1 啦

套路颓式子，请左转 [BZOJ1101Zap](https://www.lydsy.com/JudgeOnline/problem.php?id=1101)

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int M = 1e7 + 10;
typedef long long ll;
int T, n, m;
int mu[M], pri[M], cnt;
ll f[M];
bool mark[M];

void prework(int N) {
    mu[1] = 1;
    rep(i, 2, N) {
        if (!mark[i]) pri[++cnt] = i, mu[i] = -1;
        rep(j, 1, cnt) {
            if (pri[j] * i > N) break;
            mark[pri[j] * i] = 1;
            if (i % pri[j] == 0) { mu[i * pri[j]] = 0; break; }
            else mu[i * pri[j]] = -mu[i];
        }
    }
    rep(i, 1, cnt) {
        int p = pri[i];
        for (int j = 1; j * p <= N; j++) f[j * p] += mu[j];
    }
    rep(i, 1, N) f[i] += f[i - 1];
}

int main() {
    prework(1e7);
    cin >> T;
    while (T--) {
        scanf("%d%d", &n, &m);
        if (n > m) swap(n, m);
        ll ans = 0;
        for (int i = 1, j; i <= n; i = j + 1) {
            j = min(n / (n / i), m / (m / i));
            ans += (f[j] - f[i - 1]) * (n / i) * (m / i);
        }
        printf("%lld\n", ans);
    }
    return 0;
}
```