---
title: '[HDU4638]-Group'
date: 2019-02-05 00:30:40
tags: 
    - 莫队
mathjax: true
hidden: true
---

[题目链接](https://vjudge.net/problem/HDU-4638)

这题首先可以判断是莫队啦，然后怎么做呢？

O(n) 计算块数肯定是不行的了，那么我们还是要用 O(1) 的操作代替它。

维护一个 vis[] 记录 x 是否被访问过，update 的时候看看 vis[x - 1] 和 vis[x + 1] 是否被访问过。

如果 vis[x - 1] = vis[x + 1] = 1, 那么显然块数 ans -= 1；

如果其中一个为 0，其中一个为 1，那么 ans 不变。

如果 vis[x - 1] = vis[x + 1] = 1, 那么 ans += 1.

所以就是这样。

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 1e5 + 10;
int T, n, m, a[N], unit, ans, ret[N];
bool vis[N];
struct node { int l, r, id; }q[N];

bool cmp(node a, node b) {
    return a.l / unit != b.l / unit ? a.l / unit < b.l / unit : a.r < b.r;
}

void update(int x, bool t) {
    vis[x] = t;
    if (t) ans += 1 - vis[x - 1] - vis[x + 1];
    else ans += vis[x + 1] + vis[x - 1] - 1;
}

int main() {
    scanf("%d", &T);
    while (T--) {
        scanf("%d%d", &n, &m);
        unit = (int)sqrt(n);
        for (int i = 1; i <= n; i++) scanf("%d", &a[i]);
        for (int i = 1; i <= m; i++) {
            scanf("%d%d", &q[i].l, &q[i].r);
            q[i].id = i;
        }
        sort(q + 1, q + m + 1, cmp);
        memset(vis, 0, sizeof(vis));
        int L = 1, R = 0;
        ans = 0;
        for (int i = 1; i <= m; i++) {
            while (R < q[i].r) update(a[++R], 1);  // 非常重要！如果先移 L，可能会造成负数的情况，所以要先移 R
            while (R > q[i].r) update(a[R--], 0);
            while (L > q[i].l) update(a[--L], 1);
            while (L < q[i].l) update(a[L++], 0);
            ret[q[i].id] = ans;
        }
        for (int i = 1; i <= m; i++) printf("%d\n", ret[i]);
    }
    return 0;
}
```