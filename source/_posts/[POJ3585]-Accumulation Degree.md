---
title: '[POJ3585]-Accumulation Degree'
date: 2018-06-06 14:45:40
tags:
    - 树形动规
mathjax: true
---

https://vjudge.net/problem/POJ-3585

题意：有一个水系由 N - 1 条河道和 N 个交叉点组成。我们将交叉点看作树中的节点，编号为 1～N ，河道则看作树中的无向边。每条河道有一个容量，连接 x 与 y 的河道的容量为 $c(x, y)$ 。河道中单位时间流过的水量不能超过河道的容量。
有一个节点是整个水系的发源地，不停发出水，我们称之为源点。除源点之外，树中所有度数为一的节点都是入海口，可以吸收无限多的水，我们称之为汇点。水系中的水从源点出发，沿着每条河道，最终流向每个汇点。
在整个水系稳定时，每条河道中的水都以单位时间固定的水量流向固定的方向。除源点和汇点之外，其余各点不贮存水，也就是流入该点的河道水量之和等于从该点流出的河道水量之和。整个水系的流量就定义为源点单位时间发出的水量。
在流量不超过河道容量的前提下，求哪个点作为源点时，整个水系的流量最大。$N \leq 2 * 10^5$ 。

这是一个“不定根”的树形DP问题。这一类题目一般给定一个树形结构，需要以每个节点为根进行一系列统计。
我们使用”二次扫描与换根法“来解这一类题，第一次扫描任选一个点为根，在“有根树”上执行一次树形DP，也就是回溯时发生的、自底向上的状态转移。第二次扫描时，从刚才选的根出发，对整棵树执行一次深度优先遍历，进行自顶向下的推导，计算出“换根”之后的解。

再回过来看这题。以树根为源点时，每个节点都只会从父节点处获得水源，并流向它的子节点。每个节点的“流域”就是以该点为根的子树，非常符合树形DP的应用场景————每棵子树都是一个“子问题”。

设 $D[x]$ 表示在以 x 为根的子树中，把 x 作为源点，从 x 出发流向子树的流量最大是多少。

$$D[x] = \sum_{y \in Son(x)}
\begin{cases}
min(D[y], c(x, y))& if:{degree[y] > 1}\\

c(x, y)& if:{degree[y] = 1}
\end{cases}
$$

若只是单纯地枚举，那么时间复杂度为 $O(n^2)$ 。而用“二次扫描与换根法”代替源点的枚举可以在 $O(n)$ 的时间内解决整个问题。

我们任选一个源点作为根结点，即为 $root$ ，然后采用一次 DP，求出 $D_{root}$ 数组，即为 D 数组。

设 $F[x]$ 表示把 x 作为源点，流向整个水系，流量最大是多少。对于 $root$ 显然有 $F[root] = D[root]$ 。

假设 $F[x]$ 已被正确求出，考虑其子节点 y ，$F[y]$ 尚未被计算，$F[y]$ 包含两部分：

1. 从 y 流向 以 y 为根的子树的流量，即为 $D[y]$ 。
2. 从 y 沿着到父节点 x 的河道，进而流向水系中其他部分的流量。

因为把 x 作为源点的总流量为 $F[x]$ ，从 x 流向 y 的流量为 $min(D[y], c(x, y))$ ，所以从 x 流向除 y 以外的其他部分的流量就是二者之差。

$$ F[y] = D[y] +
\begin{cases}
min(F[x] - min(D[y], c(x, y)), c(x, y))& if:{degree[x] > 1}\\

c(x, y)& if:{degree[x] = 1}
\end{cases}
$$

$F[y]$ 就是把源点（树根）从 x 换位 y 后，流量的计算结果。这是一个自顶向下的递推方程，我们通过一次深度优先遍历实现。这便是“换根”的体现。

CODE：
``` c++
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <cmath>
#include <vector>
using namespace std;

const int N = 200005;
int T, n, cnt;
int D[N], F[N], degree[N], vis[N];

struct edge {
    int to, capi;
};

vector<edge> G[N];

void dp(int x) {
    vis[x] = 1;
    D[x] = 0;
    for (int i = 0; i < G[x].size(); i++) {
        edge y = G[x][i];
        if (vis[y.to]) continue;
        dp(y.to);
        if (degree[y.to] == 1) D[x] += y.capi;
        else D[x] += min(D[y.to], y.capi);
    }
}

void dfs(int x) {
    vis[x] = 1;
    for (int i = 0; i < G[x].size(); i++) {
        edge y = G[x][i];
        if (vis[y.to]) continue;
        if (degree[x] == 1) F[y.to] = D[y.to] + y.capi;
        else F[y.to] = D[y.to] + min(F[x] - min(D[y.to], y.capi), y.capi);
        dfs(y.to);
    }
}

int main() {
    scanf("%d", &T);
    while (T--) {
        memset(F, 0, sizeof(F));
        memset(D, 0, sizeof(D));
        memset(vis, 0, sizeof(vis));
        memset(degree, 0, sizeof(degree));
        scanf("%d", &n);
        for (int i = 1; i <= n; i++) G[i].clear();
        int x, y, z;
        cnt = 0;
        for (int i = 1; i < n; i++) {
            scanf("%d%d%d", &x, &y, &z);
            degree[x]++, degree[y]++;
            G[x].push_back(edge{ y, z });
            G[y].push_back(edge{ x, z });
        }
        int root = 1;
        dp(root);
        memset(vis, 0, sizeof(vis));
        F[root] = D[root];
        dfs(root);
        int ans = 0;
        for (int i = 1; i <= n; i++) ans = max(ans, F[i]);
        printf("%d\n", ans);
    }
    return 0;
}
```

换根以前也碰到过，当时想了一天，从此对树形DP产生了阴影。。。现在看起来还是很妙的，值得掌握！