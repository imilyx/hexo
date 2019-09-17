---
title: 'Atcoder Beginner Contest 141'
date: 2019-09-17 22:39:40
tags: 
    - 比赛
mathjax: true
---

[传送门](https://atcoder.jp/contests/abc141/tasks)

ABC皆为水题。。。略过

### D - Powerful Discount Tickets
-----

a/b/c(下取整) 竟然等于 a/(bc)(下取整)。。。比赛时想到正解做法，但由于无法证明前面那句也举不出反例而不了了之了。。。

正解就是堆优化啦

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 1e5 + 10;
typedef long long ll;
int n, m, a[N], f[N][2];
priority_queue<int> q;
ll ans;

int main() {
    cin >> n >> m;
    rep(i, 1, n) {
        scanf("%d", &a[i]);
        q.push(a[i]);
    }
    while (m--) {
        int x = q.top(); q.pop();
        x /= 2;
        q.push(x);
    }
    while (q.size()) {
        int x = q.top(); q.pop();
        ans += x;
    }
    printf("%lld\n", ans);
    return 0;
}
```

### E - Who Says a Pun?
-----

后缀数组 + 二分经典好题（套路题），可见 POJ1743

### F - Xor Sum 3
-----

一句话题意：设序列的异或值为 sum，求分两堆，一堆异或值为 res，使得 res + (sum ^ res) 最大。

一看到就觉得是线性基。。。然鹅并不会做，果然还是太弱了。。。

位运算的题十有八九要按位讨论！来一起想想，若 sum 的第 i 位为 1，那么分的两堆中必一堆为 1 一堆为 0，也就是说应忽略 i 位为 1 的位；若 i 位为 0，那么两堆中这一位的贡献是相同的，也就是说我们只要找 i 位为 0 的数的最大异或值就好啦。线性基可以胜任。

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 1e5 + 10;
typedef long long ll;
int n, q[65];
ll a[N], sum, tot, p[65];

void insert(ll x) {
    rep(i, 1, tot) {
        if (!(x & (1ll << q[i]))) continue;
        if (!p[i]) { p[i] = x; break; }
        x ^= p[i];
    }
}

int main() {
    cin >> n;
    rep(i, 1, n)
        scanf("%lld", &a[i]), sum ^= a[i];
    for (int i = 60; i >= 0; i--)
        if (!((1ll << i) & sum)) q[++tot] = i;
    rep(i, 1, n) insert(a[i]);
    ll res = 0;
    rep(i, 1, tot)
        if (!(res & (1ll << q[i]))) res ^= p[i];
    printf("%lld\n", res + (sum ^ res));
    return 0;
}
```