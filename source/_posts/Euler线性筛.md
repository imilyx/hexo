---
title: Euler线性筛
date: 2018-01-21 20:02:40
tags:
    - 数学
---

### 欧拉筛法求质数
------------------

当我们知道一个数为质数时，它的倍数肯定不是质数，所以我们可以从 2 开始通过乘积筛掉所有的合数.

欧拉筛法保证所有合数都是被它的最小质因子筛掉的，时间复杂度 O(N).

代码：
``` c++
int vis[N], prime[N];
void primes(int n) {
    memset(vis, 0, sizeof(vis));
    m = 0;  // 质数数量
    for (int i = 2; i <= n; i++) {
        if (!vis[i]) {
            prime[++m] = i;
        }
        for (int j = 1; j <= m; j++) {
            if (prime[j] * i > n) break;
            vis[i * prime[j]] = 1;
            if (i % prime[j] == 0) break;  // 保证每个数只会被它的最小质因子筛去
        }
    }
}
```