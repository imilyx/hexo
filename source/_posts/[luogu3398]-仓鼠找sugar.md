---
title: '[LG3398]-仓鼠找sugar'
date: 2019-03-03 23:15:40
tags: 
    - LCA
mathjax: true
hidden: true
---

[题目链接](https://www.luogu.org/problemnew/show/P3398)

一开始是想找 Tarjan，然而被这题“骗”了 qwq 这题的 Tarjan 只是 LCA 来着

不过看这题很经典的样子，做了感觉蛮有意思！曾经 LJ 讲过，树的题目十有八九都是 LCA，果然没有错。。

应该是个结论题，那么就不猜啦，我直接说结论：设 LCA(a, b) = x, LCA(c, d) = y. 若两条路径相交，则必有 x 在 c ~ d 路径上或 y 在 a ~ b 路径上。

以下是证法：

假设两条路径相交。

容易知道，两点的 LCA 必定在两点路径上。设两条路径相交的路段为 e ~ f，且 x 不在 c ~ d 上，且 e ~ f 在 x ~ b 上。（根据对称性可知，这样是没有毛病滴）

因为 b ~ e, e ~ f, f ~ x 深度递减，c ~ f, d ~ e 深度递减，则 f 必定为 LCA(c, d)，建议画图理解下！

大概就是这个意思。然后，现在的问题就成了如何判断点 k 是否在 u ~ v 的路径上呢？这也很简单，若 dis(u, k) + dis(k, v) == dis(u, v) 则 k 在 u ~ v 上啦。是不是有点像最短路？

code :
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 1e5 + 10;
int n, m, dep[N], fa[N][70];
int to[N << 1], nxt[N << 1], lnk[N], cnt;

void add(int x, int y, int cnt) {
    to[cnt] = y, nxt[cnt] = lnk[x], lnk[x] = cnt;
}

void dfs(int x, int f) {
    for (int i = 1; i <= 60; i++)
        fa[x][i] = fa[fa[x][i - 1]][i - 1];
    for (int i = lnk[x]; i; i = nxt[i]) {
        int y = to[i];
        if (y == f) continue;
        dep[y] = dep[x] + 1;
        fa[y][0] = x;
        dfs(y, x);
    }
}

int lca(int x, int y) {
    if (dep[x] < dep[y]) swap(x, y);
    for (int i = 60; i >= 0; i--)
       if (dep[fa[x][i]] >= dep[y]) x = fa[x][i];
    if (x == y) return x;
    for (int i = 60; i >= 0; i--)
        if (fa[x][i] != fa[y][i]) x = fa[x][i], y = fa[y][i];
    return fa[x][0];
}

int dis(int x, int y) {
    int z = lca(x, y);
    return abs(dep[x] - dep[z]) + abs(dep[y] - dep[z]);
}

int main() {
    scanf("%d%d", &n, &m);
    for (int i = 1; i < n; i++) {
        int x, y; scanf("%d%d", &x, &y);
        add(x, y, i), add(y, x, i + n);
    }
    dfs(1, 0);
    while (m--) {
        int a, b, c, d;
        scanf("%d%d%d%d", &a, &b, &c, &d);
        int x = lca(a, b), y = lca(c, d);
        if (x == 0) x = 1;
        if (y == 0) y = 1;
        if (dis(a, y) + dis(b, y) == dis(a, b) || dis(c, x) + dis(x, d) == dis(c, d)) puts("Y");
        else puts("N");
    }
    return 0;
}
```