---
title: "[AT1984]-Wide Swap"
date: 2020-02-09 19:15:40
tags: 
    - 线段树
    - 拓扑排序
mathjax: true 
---

[传送门](https://vjudge.net/problem/AtCoder-1984)

这题太神了！！！我爱死 atcoder 了！！！！！

[这位大佬讲的好，我再补充些自己的理解](https://www.cnblogs.com/cjoierShiina-Mashiro/p/12233486.html)

总结出来解题的常用步骤，也就是最重要的技能是 **转化** 和 **找性质**。

好！本题必须转化。本来必须满足两条限制（见题目），但大佬这么一转化就限定好只能交换相邻位置，两条限制一下变成一条了！

求 p 最小字典序等同于求 q 最小字典序，为什么？q_{pi} 表达的是什么？

q_{pi} 表示 pi 目前放的位置。

q 的下标从小到大递增。对于 pi < pj, q_{pi} < q_{pj}（即更小的值位置更前）显然是比较优的。所以求 p 最小字典序等同于求 q 最小字典序。

q 中，如果 1 <= i, j <= n, |qi - qj| < K, 显然 qi 和 qj 的相对位置不会发生变化。存在着很多诸如此类的限制，我们又想要 q 字典序最小，怎么办呢？

最小拓扑序。对于 1 <= i < j <= n, |qi - qj| < K, 从 qi 向 qj 连一条边，这样不仅满足了所有限制，也使 q 的字典序最小了。**这是套路，一定要掌握！！！**

code :
``` c++
// 解题常用步骤：转化 找性质
// 本题第一步转化起到了简化的作用
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 5e5 + 10, inf = 0x3f3f3f3f;
int n, K, a[N], x, ans[N];
int mn[N << 2], deg[N];
priority_queue<int, vector<int>, greater<int> >q;  // 用 pq 来求最小拓扑序
vector<int> e[N];

void upd(int x) { mn[x] = min(mn[x << 1], mn[x << 1 | 1]); }

void modify(int x, int l, int r, int pos, int v) {
    if (l == r) { mn[x] = v; return; }
    int mid = (l + r) >> 1;
    if (pos <= mid) modify(x << 1, l, mid, pos, v);
    else modify(x << 1 | 1, mid + 1, r, pos, v);
    upd(x);
}

int query(int x, int l, int r, int lx, int rx) {
    if (rx < l || lx > r) return inf;
    if (lx <= l && r <= rx) return mn[x];
    int mid = (l + r) >> 1;
    return min(query(x << 1, l, mid, lx, rx), query(x << 1 | 1, mid + 1, r, lx, rx));
}

int main() {
    cin >> n >> K;
    rep(i, 1, n) scanf("%d", &x), a[x] = i;
    memset(mn, 0x3f, sizeof(mn));
    for (int i = n, j; i >= 1; i--) {
        if ((j = query(1, 1, n, a[i] + 1, a[i] + K - 1)) != inf)
            ++deg[a[j]], e[a[i]].push_back(a[j]);
        if ((j = query(1, 1, n, a[i] - K + 1, a[i] - 1)) != inf)
            ++deg[a[j]], e[a[i]].push_back(a[j]);
        modify(1, 1, n, a[i], i);
    }
    rep(i, 1, n) if (!deg[i]) q.push(i);
    int tot = 0;
    while (q.size()) {
        int x = q.top(); q.pop();
        a[++tot] = x;
        for (int i = 0; i < e[x].size(); i++) {
            int y = e[x][i];
            if (!(--deg[y])) q.push(y); 
        }
    }
    rep(i, 1, n) ans[a[i]] = i;
    rep(i, 1, n) printf("%d\n", ans[i]);
    return 0;
}
```