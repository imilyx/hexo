---
title: '[CF241E]-Flights'
date: 2019-07-31 20:30:40
tags:
    - 差分
---

[传送门](http://codeforces.com/problemset/problem/241/E)

题意：给定 n(≤1000) 个点 m(≤5000) 条边的有向无环图，要求给边赋权 1 或 2，使 1 -> n 所有路径上的边权和相等，输出方案或无解。所有边从编号小的指向编号大的。

对于一条从 u 到 v 的边，我们可以列出不等式：1 <= dis[v] - dis[u] <= 2

处理得：dis[u] <= dis[v] - 1      dis[v] <= dis[u] + 2

跑差分约束就行。

需要注意的，在此之前需要排除那些不在 1 -> n 路径上的点。

若不排除，则可以举出反例：连接 1 和 n 的路径是有解的，可不连接 1 和 n 的路径是成环（即无解）的，那么跑 spfa 的时候就会因部分路径的无解而判整个图无解，这是不对滴！

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 5e3 + 10;
int n, m, u[N], v[N], dis[N], tot[N];
bool vis[N], mark[N];
int to[N << 1], nxt[N << 1], lnk[N], cnt, val[N << 1];
queue<int> q;

void add(int x, int y, int w) {
    to[++cnt] = y, nxt[cnt] = lnk[x], lnk[x] = cnt, val[cnt] = w;
}

void dfs(int x) {
    vis[x] = 1;
    if (x == n) { mark[x] = 1; return; }
    for (int i = lnk[x]; i; i = nxt[i])
        if (to[i] > x) {  // 是实边，而不是差分约束多加的边
            if (!vis[to[i]]) dfs(to[i]);
            if (mark[to[i]]) mark[x] = 1;
        }
}

void spfa() {
    q.push(1);
    memset(vis, 0, sizeof(vis));
    memset(dis, 0x3f, sizeof(dis));
    vis[1] = dis[1] = 0;
    while (!q.empty()) {
        int x = q.front(); q.pop(); vis[x] = 0;
        for (int i = lnk[x]; i; i = nxt[i]) {
            int y = to[i];
            if (dis[y] > val[i] + dis[x] && mark[y]) {
                dis[y] = val[i] + dis[x];
                if (!vis[y]) {
                    vis[y] = 1;
                    q.push(y);
                    ++tot[y];
                    if (tot[y] > n) { puts("No"); return; }
                }
            }
        }
    }
    puts("Yes");
    rep(i, 1, m)
        if (dis[u[i]] - dis[v[i]] == -1) puts("1");
        else puts("2");
}

int main() {
    cin >> n >> m;
    rep(i, 1, m) {
        cin >> u[i] >> v[i];
        add(u[i], v[i], 2), add(v[i], u[i], -1);
    }
    dfs(1);
    spfa();
    return 0;
}
```