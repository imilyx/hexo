---
title: '[POJ3694]-Network'
date: 2019-03-03 23:10:40
tags: 
    - 图的连通
mathjax: true
hidden: true
---

[题目链接](https://vjudge.net/problem/POJ-3694)

先用 Tarjan 算法求出无向图中所有的边双连通分量（e-DCC），并对每个 e-DCC 缩点，得到一棵树，其中树上每个节点都是一个 e-DCC, 每条边都是桥。起初桥的总数为缩点后的图中的边数。设点 x 缩点后属于第 c[x] 个 e-DCC.

考虑每次加边操作 (x, y), 若 x、y 同属一个 e-DCC, 则加入该边后，图中桥的数量不变。

否则，在缩点后得到的树上，c[x] 和 c[y] 之间的所有边都不再是桥，因为它们处在同一个环内。假设 c[x] 和 c[y] 之间有 cnt 条边，那么把图中桥的总数减去 cnt 即可，同时不要忘了标记这 cnt 条边已经不是桥了。

然后...不要用 cin 啊！！！会 T (哭

一定要用 scanf...

code :
``` c++
#include <cstdio>
#include <algorithm>
#include <vector>
#include <cstring>
#include <iostream>
#define cl(x) memset(x, 0, sizeof(x))
using namespace std;

const int N = 1e5 + 10, M = 2e5 + 10;
int n, m, Q, ans, dcc, Case;
int dfn[N], low[N], vis[N], fa[N], tot, dep[N], c[N];
int to[M << 1], nxt[M << 1], lnk[N], cnt, mark[M << 1];
bool bridge[M << 1];
vector<int> e[N];

void add(int u, int v) {
    to[++cnt] = v, nxt[cnt] = lnk[u], lnk[u] = cnt, mark[cnt] = 1;
}

void tarjan(int x, int edge) {
    dfn[x] = low[x] = ++tot;
    for (int i = lnk[x]; i; i = nxt[i]) {
        int y = to[i];
        if (!dfn[y]) {
            tarjan(y, i);
            low[x] = min(low[x], low[y]);
            if (dfn[x] < low[y]) // 注意，因为要求割边，从 y 以及 y 的子树中所能到达的只能说 x 的子树（不包括 x），这样 (x, y) 才是割边
                bridge[i] = bridge[i ^ 1] = 1;
        } else if (i != (edge ^ 1))
            low[x] = min(low[x], dfn[y]);
    }
}

void dfs(int x) {
    c[x] = dcc;
    for (int i = lnk[x]; i; i = nxt[i]) {
        int y = to[i];
        if (c[y] || bridge[i]) continue;
        dfs(y);
    }
}

void through(int x) {
    vis[x] = 1;
    for (int i = 0; i < (int)e[x].size(); i++) {
        int y = e[x][i];
        if (vis[y]) continue;
        if (c[x] != c[y]) {
            dep[c[y]] = dep[c[x]] + 1;
            add(c[y], c[x]);
            fa[c[y]] = cnt;  // fa[x] 表示 x 上面那条边的编号
        }
        through(y);
    }
}

void lca(int x, int y) {
    if (dep[x] < dep[y]) swap(x, y);
    while (dep[x] > dep[y]) {
        int t = fa[x];
        if (mark[t]) ans--, mark[t] = 0;
        x = to[fa[x]];
    }
    if (x == y) return;
    while (to[fa[x]] != to[fa[y]]) {
        int t = fa[x];
        if (mark[t]) ans--, mark[t] = 0;
        
        t = fa[y];
        if (mark[t]) ans--, mark[t] = 0;

        x = to[fa[x]], y = to[fa[y]];
    }
    int t = fa[x];
    if (mark[t]) ans--, mark[t] = 0;
        
    t = fa[y];
    if (mark[t]) ans--, mark[t] = 0;
    return;
}

int main() {
    while (scanf("%d%d", &n, &m) && n && m) {
        cnt = 1;
        cl(lnk), cl(e);
        for (int i = 1; i <= m; i++) {
            int x, y; scanf("%d%d", &x, &y);
            add(x, y), add(y, x);
            e[x].push_back(y), e[y].push_back(x);
        }

        cl(bridge), cl(dfn), cl(c);
        tot = ans = dcc = 0;
        fa[1] = 1;
        for (int i = 1; i <= n; i++) if (!dfn[i]) tarjan(i, 0);
        for (int i = 1; i <= n; i++) if (!c[i]) ++dcc, dfs(i);

        cl(lnk), cl(mark), cl(vis);
        cnt = 1;
        through(1);
        ans = dcc - 1;
        printf("Case %d:\n", ++Case);
        
        scanf("%d", &Q);
        while (Q--) {
            int x, y; scanf("%d%d", &x, &y);
            if (c[x] == c[y]) { printf("%d\n", ans); continue; }
            lca(c[x], c[y]);
            printf("%d\n", ans);
        }
        puts("");
    }
    return 0;
}
```