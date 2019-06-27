---
title: 动态DP
date: 2019-06-27 17:40:40
tags: 
    - 树链剖分
    - 矩阵乘法
mathjax: true
---

神仙芝士= =

恐怕讲不好，还是挂别人 dalao 的 blog 吧！

[这个超好](https://www.cnblogs.com/RabbitHu/p/9112811.html)

再挂个板子
``` c++
// luogu4719
#include <bits/stdc++.h>
using namespace std;

const int N = 1e5 + 10, inf = 1e9;
int n, m, dfn;
int f[N][2], g[N][2];
int w[N], w2[N], sz[N], top[N], ed[N], bel[N];
int dep[N], fa[N], son[N], id[N];
int to[N << 1], nxt[N << 1], lnk[N], cnt;
struct tree {
    int l, r;
}tr[N << 2];
struct node {
    int m[5][5];
    void cl() {
        for (int i = 0; i <= 2; i++)
            for (int j = 0; j <= 2; j++) m[i][j] = -inf;
    }
}ans[N << 2], val[N << 2];

void add(int x, int y) {
    to[++cnt] = y, nxt[cnt] = lnk[x], lnk[x] = cnt;
}

void dfs1(int x, int f) {
    sz[x] = 1;
    dep[x] = dep[f] + 1;
    fa[x] = f;
    int Mx = -1;
    for (int i = lnk[x]; i; i = nxt[i]) {
        int y = to[i];
        if (y == f) continue;
        dfs1(y, x);
        sz[x] += sz[y];
        if (sz[y] > Mx) Mx = sz[y], son[x] = y;
    }
}

void dfs2(int x, int f) {
    top[x] = f;
    id[x] = ++dfn;
    bel[dfn] = x, ed[f] = x;
    if (son[x]) dfs2(son[x], f);
    for (int i = lnk[x]; i; i = nxt[i]) {
        int y = to[i];
        if (y == fa[x] || y == son[x]) continue;
        dfs2(y, y);
    }
    ed[x] = ed[f]; // !!!!!!!!
}

void dfs3(int x) {
    g[x][1] = w[x];
    for (int i = lnk[x]; i; i = nxt[i]) {
        int y = to[i];
        if (y == son[x] || y == fa[x]) continue;
        dfs3(y);
        g[x][0] += max(f[y][0], f[y][1]);
        g[x][1] += f[y][0];
    }
    f[x][0] += g[x][0], f[x][1] += g[x][1];
    if (son[x]) {
        dfs3(son[x]);
        f[x][0] += max(f[son[x]][1], f[son[x]][0]);
        f[x][1] += f[son[x]][0];
    }
}

// ----------------------------------------------------------------

node mul(node x, node y) {
    node res;
    res.cl();
    for (int i = 0; i <= 1; i++)
        for (int j = 0; j <= 1; j++)
            for (int k = 0; k <= 1; k++)
                res.m[i][j] = max(res.m[i][j], x.m[i][k] + y.m[k][j]);
    return res;
}

void build(int x, int l, int r) {
    tr[x].l = l, tr[x].r = r;
    if (l == r) {
        val[bel[l]].m[0][0] = g[bel[l]][0], val[bel[l]].m[0][1] = g[bel[l]][0];
        val[bel[l]].m[1][0] = g[bel[l]][1], val[bel[l]].m[1][1] = -inf;
        ans[x] = val[bel[l]];
        return;
    }
    int mid = (l + r) >> 1;
    build(x << 1, l, mid), build(x << 1 | 1, mid + 1, r);
    ans[x] = mul(ans[x << 1], ans[x << 1 | 1]);
}

node query(int x, int l, int r) {
    if (l <= tr[x].l && tr[x].r <= r) return ans[x];
    int mid = (tr[x].l + tr[x].r) >> 1;
    if (mid >= r) return query(x << 1, l, r);
    if (mid < l) return query(x << 1 | 1, l, r);
    return mul(query(x << 1, l, r), query(x << 1 | 1, l, r));
}

void upd(int x, int pos) {
    if (tr[x].l == tr[x].r && tr[x].l == pos) {
        ans[x] = val[bel[pos]];
        return;
    }
    int mid = (tr[x].l + tr[x].r) >> 1;
    if (pos <= mid) upd(x << 1, pos);
    else upd(x << 1 | 1, pos);
    ans[x] = mul(ans[x << 1], ans[x << 1 | 1]);
}

void modify(int x, int f) {
    val[x].m[1][0] += f - w[x];
    w[x] = f;
    while (x != 0) {
        int y = top[x];
        node lst = query(1, id[y], id[ed[y]]);
        upd(1, id[x]);
        node now = query(1, id[y], id[ed[y]]);
        x = fa[y];
        val[x].m[0][0] += max(now.m[0][0], now.m[1][0]) - max(lst.m[0][0], lst.m[1][0]);
        val[x].m[0][1] = val[x].m[0][0];
        val[x].m[1][0] += now.m[0][0] - lst.m[0][0];
    }
}

int main() {
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n; i++) scanf("%d", &w[i]);
    for (int i = 1; i < n; i++) {
        int x, y; scanf("%d%d", &x, &y);
        add(x, y), add(y, x);
    }
    dfs1(1, 0);
    dfs2(1, 0);
    dfs3(1);
    build(1, 1, n);
    while (m--) {
        int x, y; scanf("%d%d", &x, &y);
        modify(x, y);
        node res = query(1, id[1], id[ed[1]]);
        printf("%d\n", max(res.m[0][0], res.m[1][0]));
    }
    return 0;
}
```