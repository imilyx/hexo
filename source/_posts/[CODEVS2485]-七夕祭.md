---
title: '[CODEVS2485]-七夕祭'
date: 2018-08-17 22:10:40
tags: 
    - 贪心
mathjax: true
hidden: true
---

[题目链接](http://codevs.cn/problem/2485/)

题意略。

这是一道环形均分纸牌的模板题。

可以发现，两行之间摊点互相交换是不会影响所对应的列的。所以我们可以对行和列单独考虑。

环形均分纸牌中，M 人的纸牌数、前缀和分别是: (A[1], S[1]), (A[2], S[2]), ... , (A[M], S[M]). 设纸牌总数为 T。

如果在第 k 个人后把环断开，那么这 M 个人的纸牌数、前缀和分别是: (A[k + 1], S[k + 1] - S[k]), (A[k + 2], S[k + 2] - S[k]), ... (A[M], S[M] - S[k]), (A[1], S[1] + S[M] - S[k]), ... (A[k], S[k] + S[M] - S[k]).

原问题等价于每个人手里纸牌数减去 T / M, 目标是所有人的纸牌数都为 0 的最小代价，即 S[M] = 0, 也就是说，在第 k 个人后把环断开，前缀和数组的变化是都减去 S[k].

那么我们就是要求 $\sum\limits_{i = 1}^M|S[i] - S[k]|$. 根据货仓选址问题我们得知，$k = \frac{M + 1}{2}$. 这是最优解。时间复杂度 $O(N\ log\ N + M\ log\ M)$.

想了想认为环形处理很妙！先是断环为链，然后通过意思不变的转化与前缀和证明算法是“货仓选址”，用了最简单的算法实现。

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 1e5 + 10;
typedef long long ll;
ll n, m, t;
ll r[N], c[N], s[N];

ll calc(ll a[], ll n) {
    for (int i = 1; i <= n; i++) a[i] -= a[0] / n, s[i] = s[i - 1] + a[i];
    sort(s + 1, s + n + 1);
    ll ans = 0;
    for (int i = 1; i <= n; i++) ans += abs(s[i] - s[(n + 1) >> 1]);
    return ans;
}

int main() {
    scanf("%lld%lld%lld", &n, &m, &t);
    for (int i = 1; i <= t; i++) {
        int x, y;
        scanf("%d%d", &x, &y);
        r[x]++, c[y]++;
    }
    for (int i = 1; i <= n; i++) r[0] += r[i];
    for (int i = 1; i <= m; i++) c[0] += c[i];
    if (r[0] % n == 0 && c[0] % m == 0)
        cout << "both " << calc(r, n) + calc(c, m) << endl;
    else if (r[0] % n == 0)
        cout << "row " << calc(r, n) << endl;
    else if (c[0] % m == 0)
        cout << "column " << calc(c, m) << endl;
    else cout << "impossible" << endl;
    return 0;
}
```