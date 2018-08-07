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
int v[N], prime[N];  // v[i] 储存 i 的最小质因子
void primes(int n) {
    memset(v, 0, sizeof(v));  // 最小质因子
    m = 0;  // 质数数量
    for (int i = 2; i <= n; i++) {
        if (v[i] == 0) {
            v[i] = i, prime[++m] = i;
        }
        for (int j = 1; j <= m; j++) {
            if (prime[j] > v[i] || prime[j] > n / i) break;
            v[i * prime[j]] = prime[j];
        }
    }
    for (int i = 1; i <= m; i++) cout << prime[i] << endl;
}
```