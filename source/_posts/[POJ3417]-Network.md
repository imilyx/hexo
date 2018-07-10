---
title: '[POJ3417]-Network'
date: 2018-07-10 20:56:40
tags: 
    - 最近公共祖先
mathjax: true
---

https://vjudge.net/problem/POJ-3417

题意：有一张 n 个节点的无向图，n - 1 条主要边、 m 条附加边。主要边构成一棵树，附加边则是非树边，把一条附加边 (x, y) 添加到主要边构成的树中，会与树上 x, y 之间的路径一起形成一个环。你要使图分为两部分，即刚好切断一条主要边和一条附加边。

考虑一条附加边 (x, y) ，我们可以称附加边 (x, y) 都把树上 x、y 之间的路径上的每条边“覆盖了一次”，我们只需统计出每一条主要边被覆盖了多少次。若第一步把被覆盖 0 次的主要边切断，则第二步可任意切断一条附加边。若第一步把被覆盖 1 次的主要边切断，则第二步唯一。若第一步把被覆盖 2 次及 2 次以上的主要边切断，则第二步无论如何操作都无法成功。

记录覆盖次数有一个很好的办法：树上差分算法，即将普通差分的区间增减变为树上的节点增减。具体操作为对于每条非树边 (x, y) ，令节点 x 的权值加一，节点 y 的权值加一，节点 LCA(x, y) 减二，最后进行深度优先遍历计算前缀和。时间复杂度 $O(n + m)$ 。

主要就是注意树上差分算法的思想啦。。

最后提一个很╮(╯▽╰)╭的事情就是我用了 ios::sync_with_stdio(false) 和 cin，竟然TLE了。。POJ真是神奇的网站。。

CODE：
``` c++
#include <cstdio>
#include <cstring>
#include <iostream>
#include <cmath>
#include <algorithm>
#include <queue>
using namespace std;

const int N = 200005;
int n, m, t;
int to[N << 1], nxt[N << 1], lnk[N], cnt;
int f[N][20], d[N], s[N];
bool vis[N];
queue<int> q;

void add(int u, int v) {
    to[++cnt] = v, nxt[cnt] = lnk[u], lnk[u] = cnt;
    to[++cnt] = u, nxt[cnt] = lnk[v], lnk[v] = cnt;
}

void bfs() {
    q.push(1);
    d[1] = 1;
    while (!q.empty()) {
        int x = q.front();
        q.pop();
        for (int i = lnk[x]; i; i = nxt[i]) {
            int y = to[i];
            if (d[y]) continue;
            d[y] = d[x] + 1;
            f[y][0] = x;
            for (int j = 1; j <= t; j++) f[y][j] = f[f[y][j - 1]][j - 1];
            q.push(y);
        }
    }
}

int lca(int x, int y) {
    if (d[x] > d[y]) swap(x, y);
    for (int i = t; i >= 0; i--)
        if (d[f[y][i]] >= d[x]) y = f[y][i];
    if (x == y) return x;
    for (int i = t; i >= 0; i--)
        if (f[x][i] != f[y][i]) x = f[x][i], y = f[y][i];
    return f[x][0];
}

void pre_add(int x) {
    vis[x] = true;
    for (int i = lnk[x]; i; i = nxt[i]) {
        int y = to[i];
        if (!vis[y]) {
            pre_add(y);
            s[x] += s[y];
        }
    }
}

int main() {
    // ios::sync_with_stdio(false);
    scanf("%d%d", &n, &m);
    t = (log(n) / log(2)) + 1;  // eg: log(8) / log(2) = 3
    for (int i = 1; i < n; i++) {
        int u, v;
        scanf("%d%d", &u, &v);;;
        add(u, v);
    }
    bfs();
    for (int i = 1; i <= m; i++) {
        int u, v;
        scanf("%d%d", &u, &v);
        int uv = lca(u, v);
        s[u]++, s[v]++, s[uv] -= 2;
    }
    memset(vis, false, sizeof(vis));
    pre_add(1);
    int ans = 0;
    for (int i = 2; i <= n; i++) {
        if (s[i] == 0) ans += m;
        else if (s[i] == 1) ans++;
    }
    cout << ans << endl;
    return 0;
}
```