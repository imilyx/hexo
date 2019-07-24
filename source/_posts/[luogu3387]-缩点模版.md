---
title: '[LG3387]-缩点模版'
date: 2019-02-22 17:58:40
tags: 
    - 缩点
mathjax: true
---

[题目链接](https://www.luogu.org/problemnew/show/P3387)

人生第一篇缩点啊！

话说几年前看别人博客，看到他们说“进NOIP考场前在调缩点”就觉得缩点是很了不起的东西，刚好学了Tarjan，就来试一试了（虽然是模版题

首先要知道此题为什么要用缩点。路径可以重复走，所以不用担心重复这件事。我们发现了一个贪心：当走到一个点 a 时，a 所在的环里的所有点都可以走到（不走白不走！） 更准确一些，就是 a 所在的所有强连通分量里的点都可以到达了，这样我们找出所有的强连通分量再来个记忆化搜索（搜索量会小很多），就可以 AC 啦～

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 1e5 + 1e4, M = 1e5 + 1e4;
int dfn[N], low[N], vis[N], cnt;
int tot, color[N], sum[N], f[N];  // tot 表示缩了后有多少个点
int lnk[N], nxt[M], to[M], edge;
int stk[N], top;
int n, m, val[N], x[N], y[N], ans;

void add(int u, int v) {
    to[++edge] = v, nxt[edge] = lnk[u], lnk[u] = edge;
}

void tarjan(int x) {
    stk[++top] = x;
    vis[x] = 1;
    dfn[x] = low[x] = ++cnt;
    for (int i = lnk[x]; i; i = nxt[i]) {
        int y = to[i];
        if (!dfn[y]) {
            tarjan(y);
            low[x] = min(low[x], low[y]);
        } else if (vis[y]) {
            low[x] = min(low[x], dfn[y]);
        }
    }
    if (dfn[x] == low[x]) {
        tot++;
        while (stk[top + 1] != x) {  // 弹栈操作
            color[stk[top]] = tot;
            sum[tot] += val[stk[top]];
            vis[stk[top--]] = 0;
        }
    }
}

void search(int x) {
    if (f[x]) return;
    f[x] = sum[x];
    int maxsum = 0;
    for (int i = lnk[x]; i; i = nxt[i]) {
        if (!f[to[i]]) search(to[i]);
        maxsum = max(maxsum, f[to[i]]);
    }
    f[x] += maxsum;
}

int main() {
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n; i++) scanf("%d", &val[i]);
    for (int i = 1; i <= m; i++) {
        scanf("%d%d", &x[i], &y[i]);
        add(x[i], y[i]);
    }
    for (int i = 1; i <= n; i++) if (!dfn[i]) tarjan(i);
    memset(lnk, 0, sizeof(lnk));
    memset(to, 0, sizeof(to));
    memset(nxt, 0, sizeof(nxt));
    edge = 0;
    for (int i = 1; i <= m; i++)  // 重新建图
        if (color[x[i]] != color[y[i]])
            add(color[x[i]], color[y[i]]);
    for (int i = 1; i <= tot; i++)
        if (!f[i]) search(i), ans = max(ans, f[i]);
    printf("%d\n", ans);
    return 0;
}
```