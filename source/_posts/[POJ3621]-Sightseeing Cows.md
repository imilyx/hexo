---
title: '[POJ3621]-Sightseeing Cows'
date: 2018-08-04 15:50:40
tags: 
    - 0/1分数规划
    - 负环判定
mathjax: true
hidden: true
---

https://vjudge.net/problem/POJ-3621

题意：给定一张有向图，每个点都有一个权值 fun[i]，每条边都有一个权值 time[i]，求图中的一个环，使“环上各点的权值之和”除以“环上各边的权值之和”最大。输出这个最大值。

这是一道0/1分数规划题目，二分答案。设二分的值为 mid。

我们将一个有 t 个节点、t 条边的环记为其中的点和边：$S = (\{v_i\}, \{e_i\})$。

1. 如果图中存在一个环 S，使得 $\sum_{i = 1}^t (mid * time[e_i] - fum[v_i]) < 0$，那么可知：

$$\exists S = (\{v_i\}, \{e_i\}), mid < \frac{\sum_{i = 1}^t fun[v_i]}{\sum_{i = 1}^t time[e_i]}$$

也就是说，本题所求的最大值一定大于 mid。

2. 如果对图中任意的环 S，都有 $\sum_{i = 1}^t (mid * time[e_i] - fun[v_i]) \geq 0$，同理可知：

$$\forall S = (\{v_i\}, \{e_i\}), mid \geq \frac{\sum_{i = 1}^t fun[v_i]}{\sum_{i = 1}^t time[e_i]}$$

也就是说，本题所求的最大值不超过 mid。

综上所述，对于每轮二分，我们建立一张新图，结构与原图相同，但是没有点权，有向边 $e = (x, y)$ 的权值是 $mid * time[e] - fun[x]$。

在这张新图上，$\sum_{i = 1}^t (mid * time[e_i] - fun[v_i]) < 0$ 的含义是图中存在负环，因此，我们用 SPFA 求最短路，若有负环，则说明 mid 比答案小，令 l = mid；若最短路求解正常结束，则令 r = mid。二分结束时就求出了本题的答案。

code:
``` c++
#include <cstdio>
#include <cstring>
#include <queue>
#include <algorithm>
#define inf 10000000000000
using namespace std;

const int N = 1005, M = 5005;
int L, P;
int f[N], inq[N], inq_count[N];
int to[M << 1], nxt[M << 1], lnk[N], cnt, val[M << 1];
double dis[M];
queue<int> q;

void add(int u, int v, int w) {
    to[++cnt] = v, nxt[cnt] = lnk[u], lnk[u] = cnt, val[cnt] = w;
}

bool spfa(double limit) {
    for (int i = 1; i <= L; i++) dis[i] = inf, inq[i] = 0, inq_count[i] = 0;
    dis[1] = 0;
    inq[1] = 1;
    inq_count[1]++;
    q.push(1);
    while (!q.empty()) {
        int u = q.front();
        q.pop();
        inq[u] = 0;
        for (int i = lnk[u]; i; i = nxt[i]) {
            int v = to[i];
            double newdis = limit * val[i] - f[v];
            if (dis[v] > dis[u] + newdis) {
                dis[v] = dis[u] + newdis;
                if (!inq[v]) {
                    inq[v] = 1;
                    q.push(v);
                    inq_count[v]++;
                    if (inq_count[v] >= L) return 0;
                }
            }
        }
    }
    return 1;
}

int main() {
    scanf("%d%d", &L, &P);
    for (int i = 1; i <= L; i++) scanf("%d", &f[i]);
    int u, v, w;
    for (int i = 1; i <= P; i++) {
        scanf("%d%d%d", &u, &v, &w);
        add(u, v, w);
    }
    double ans = 0, l = 0, r = 10000, mid;
    while (r - l >= 0.001) {
        mid = (l + r) / 2;
        if (spfa(mid)) {
            r = mid;
        } else {
            l = mid, ans = mid;
        }
    }
    printf("%.2f\n", ans);
    return 0;
}
```