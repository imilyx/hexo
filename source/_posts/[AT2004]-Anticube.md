---
title: "[AT2004]-Anticube"
date: 2020-02-10 12:15:40
tags: 
    - 贪心
mathjax: true 
---

[传送门](https://vjudge.net/problem/AtCoder-2004)

题意：从 n 个数中选最多的数使得它们两两相乘不是立方数。

[大佬博客](https://blog.csdn.net/WerKeyTom_FTD/article/details/78361615)

大致思路就是 **质因数分解 + 贪心** 了，本题重点在质因数分解！！

首先，将每个 si 看作 prod{pi ^ {ai}}. 立方数 ✖️ 立方数 = 立方数，这启发我们简化指数，即 ai %= 3.

但是质因数分解理论上时间复杂度太大了！！怎么办！！！

设 si·sj = x^3,  si·sj <= 10^20,  x <= 10^{20/3}。  x 可能有一个 [10^{10/3}, 10^{20/3}] 的质因子，但是如果这个质因子大于 1e5, si 和 sj 总共也不可能有 3 个，**所以 x 的每个质因子都是 [2, 1e5] 范围内的，其中 [10^{10/3}, 1e5] 以内的数最多只有一个。**

对于大佬最后一行，我的理解是，si 中 > 10^{10/3} 的质因子，在 [10^{10/3}, 1e5] 范围内的部分 指数只会是 1 或 2，而 > 1e5 的部分最多只有 1 个，不影响大局。

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

typedef long long ll;
const int N = 1e5 + 10;
ll n, s[N], a[N], b[N], ans;  // a[i] 存的是 s[i] 的最简数，b[i] 是 a[i] 的补数
map<ll, int> num;
map<ll, bool> vis;
int mark[N], p[N], tot;
// 拓展：10^{10/3} 以内质数有 326 个，10^{10/3}~10^5 以内质数有 9267 个

void euler(int n) {
    rep(i, 2, n) {
        if (!mark[i]) p[++tot] = i;
        for (int j = 1; j <= tot && p[j] * i <= n; j++) {
            mark[p[j] * i] = 1;
            if (i % p[j] == 0) break;
        }
    }
}

int main() {
    euler(100000);
    cin >> n;
    rep(i, 1, n) scanf("%lld", &s[i]);

    ll k, tmp;
    rep(i, 1, n) {
        k = s[i]; a[i] = b[i] = 1;
        rep(o, 1, tot) {
            if (k == 1) break;
            ll j = p[o];
            if (j * j * j >= 1e10) break;  // 10^{10/3} 以内
            if (mark[j]) continue;

            tmp = 1ll * j * j * j;
            while (k % tmp == 0) k /= tmp;  // 简化
            tmp = 1ll * j * j;
            if (k % tmp == 0) a[i] *= tmp, b[i] *= j;
            else if (k % j == 0) a[i] *= j, b[i] *= tmp;
            while (k % j == 0) k /= j;
        }
        if (k > 1) {
            tmp = (ll)sqrt(k);
            if (tmp * tmp == k) a[i] *= k, b[i] *= tmp;
            else a[i] *= k, b[i] *= k * k;
        }
    }
    rep(i, 1, n) num[a[i]]++;  // a[i] 是 s[i] 去掉指数是 3 倍数质因子的结果
    rep(i, 1, n) {
        if (vis[a[i]]) continue;
        vis[a[i]] = vis[b[i]] = 1;
        ans += (a[i] == b[i] ? 1 : max(num[a[i]], num[b[i]]));
    }
    printf("%lld\n", ans);
    return 0;
}
```