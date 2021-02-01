---
title: '[POJ3352]-Road Construction'
date: 2019-07-24 23:15:40
tags: 
    - Tarjan连通分量
mathjax: true
hidden: true
---

[传送门](https://vjudge.net/problem/POJ-3352)

题意：给你一张无向图，判断至少要加多少边，才能使任意2个点间至少有2条相互独立（无公共边）的道路。

对于边双连通分量缩点。显然什么样的图缩点后都会变成一棵树！我们发现答案就是（叶子结点数+1）/2.

code :
``` c++
#include <cstdio>
#include <algorithm>
using namespace std;

const int N = 1e3 + 10;
int n, m, bcnt;
int to[N << 1], nxt[N << 1], lnk[N], cnt = 1;
int dfn[N], low[N], tot, bridge[N << 1];
int bcc[N], vis[N], deg[N];

void add(int x, int y) {
    to[++cnt] = y, nxt[cnt] = lnk[x], lnk[x] = cnt;
}

void tarjan(int x) {
    dfn[x] = low[x] = ++tot;
    for (int i = lnk[x]; i; i = nxt[i]) {
        int y = to[i];
        if (!dfn[y]) {
            tarjan(y);
            low[x] = min(low[x], low[y]);
            if (dfn[y] > low[x]) bridge[i] = 1;
        } else {
            low[x] = min(low[x], dfn[y]);
        }
    }
}

void dfs(int x, int fa) {
    bcc[x] = bcnt;
    for (int i = lnk[x]; i; i = nxt[i]) {
        int y = to[i];
        if (bridge[i] || bcc[y]) continue;
        dfs(y, x);
    }
}

int main() {
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= m; i++) {
        int x, y; scanf("%d%d", &x, &y);
        add(x, y), add(y, x);
    }
    for (int i = 1; i <= n; i++) if (!dfn[i]) tarjan(i);
    for (int i = 1; i <= n; i++) if (!bcc[i]) {
        ++bcnt;
        dfs(i, 0);
    }
    for (int i = 1; i <= n; i++) {
        for (int j = lnk[i]; j; j = nxt[j]) {
            if (bcc[i] != bcc[to[j]]) deg[bcc[to[j]]]++;
        }
    }
    int ans = 0;
    for (int i = 1; i <= bcnt; i++) if (deg[i] == 1) {
        ans++;
    }
    printf("%d\n", (ans + 1) / 2);
    return 0;
}
```