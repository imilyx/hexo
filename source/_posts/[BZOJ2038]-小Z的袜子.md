---
title: '[BZOJ2038]-小Z的袜子'
date: 2019-02-04 16:56:40
tags: 
    - 莫队
mathjax: true
hidden: true
---

[题目链接](https://www.lydsy.com/JudgeOnline/problem.php?id=2038)

看似求概率，实则求 L 到 R 的区间内每种颜色相同的袜子的数量。可以离线处理，那就是用莫队了。

注意，要按块分左端点啊！还有，“若该概率为0则输出0/1” ！

还有，对于每次询问，最后的 $O(n)$ 查询每种颜色，可以换成每次 modify cnt数组时的操作。比如说要对 cnt[c[x]] 进行 +1 操作，因为在最后的 sum 里，它的贡献是 $cnt[c[x]] * (cnt[c[x]] - 1)$（最后统一除以2）, 设 cnt[c[x]] = w, 所以换成在 modify 时 $sum = sum - w * (w - 1) + w * (w + 1)$, 若是 -1 操作，则 $sum = sum - w * (w - 1) + (w - 1) * (w - 2)$.

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 1e6 + 10;
typedef long long ll;
ll n, m, c[N], unit, cnt[N], sum, tot, ans1[N], ans2[N];
struct node { int l, r, id; ll a, b; }q[N];

ll sqr(ll x) { return x * x; }

bool cmp(node a, node b) {
    return a.l / unit != b.l / unit ? a.l / unit < b.l / unit : a.r < b.r;
}

bool cmp_id(node a, node b) {
    return a.id < b.id;
}

void modify(ll x, int t) {
    ll tmp = cnt[c[x]];
    if (t > 0) sum = sum - tmp * (tmp - 1) + tmp * (tmp + 1);
    else sum = sum - tmp * (tmp - 1) + (tmp - 1) * (tmp - 2);
    cnt[c[x]] += t;
}

int main() {
    scanf("%d%d", &n, &m);
    unit = (int)sqrt(n);
    for (int i = 1; i <= n; i++) scanf("%d", &c[i]);
    for (int i = 1; i <= m; i++) {
        scanf("%d%d", &q[i].l, &q[i].r);
        q[i].id = i;
    }
    sort(q + 1, q + m + 1, cmp);
    int L = q[1].l, R = L - 1;
    sum = 0;
    for (int i = 1; i <= m; i++) {
        while (L > q[i].l) modify(--L, 1);
        while (L < q[i].l) modify(L++, -1);
        while (R < q[i].r) modify(++R, 1);
        while (R > q[i].r) modify(R--, -1);
        if (q[i].l == q[i].r) {
            q[i].a = 0, q[i].b = 1; continue;
        }
        q[i].a = sum;
        q[i].b = (ll)(q[i].r - q[i].l + 1) * (q[i].r - q[i].l);
        ll k = __gcd(q[i].a, q[i].b);
        q[i].a /= k, q[i].b /= k;
    }
    sort(q + 1, q + m + 1, cmp_id);
    for (int i = 1; i <= m; i++) printf("%lld/%lld\n", q[i].a, q[i].b);
    return 0;
}
```