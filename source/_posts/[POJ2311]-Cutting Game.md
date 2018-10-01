---
title: '[POJ2311]-Cutting Game'
date: 2018-10-01 14:40:40
tags:
    - 博弈
mathjax: true
---

[题目链接](https://vjudge.net/problem/POJ-2311)

题意：有一张 $n * m$ 的矩形网格纸，两名玩家轮流行动。每一次行动，可以挑选一张矩形网格纸，沿着某一行或某一列的格线，将它剪成两部分。首先剪出 $1 * 1$ 的玩家获胜。两名玩家都绝对聪明，求先手是否必胜。$1 \leq n, m \leq 200$.

博弈题，最重要的就是找出必胜态、必败态还有游戏局面。若两人都采取最佳策略，那么他俩一定不会剪出 $1 * X$ 或 $X * 1$。除了 $1 * X$ 和 $X * 1$ 外，能够剪出 $1 * 1$ 的方法必然要经过 $2 * 2$，$2 * 3$ 和 $3 * 2$，因此它们是必败态。把这三个游戏状态作为终止局面判负，剪纸游戏就符合有向图游戏的特点了。

转移状态：

$$SG(n, m) = mex(\{SG(i, m)\ xor\ SG(n - i, m), 2 \leq i \leq n / 2\}) \cup \{SG(n, i)\ xor\ SG(n, m - i), 2 \leq i \leq m / 2\}$$

秃然感到铜壶灌顶（？）！SG函数还是很形象滴，=0就表示是必败态，>0就是必胜态。mex函数很好地b表示，若一个状态后继有必败态，那么这个状态就是必胜态；若一个状态后继全是必胜态，那么它就是必败态（0）。

code:
``` c++
#include <cstdio>
#include <cstring>
using namespace std;

const int N = 210;
int sg[N][N];

int SG(int n, int m) {
    if (sg[n][m] != -1) return sg[n][m];
    int vis[1010];
    memset(vis, 0, sizeof(vis));
    for (int i = 2; i + i <= n; i++)  //i 从 2 开始，其实就是使 sg[2,2] sg[2,3] sg[3,2] = 0
        vis[SG(i, m) ^ SG(n - i, m)] = 1;
    for (int i = 2; i + i <= m; i++)
        vis[SG(n, i) ^ SG(n, m - i)] = 1;
    for (int i = 0; ; i++)
        if (!vis[i]) return sg[n][m] = i;
}

int main() {
    int n, m;
    memset(sg, -1, sizeof(sg));
    while (~scanf("%d%d", &n, &m)) {
        if (SG(n, m)) puts("WIN");
        else puts("LOSE");
    }
    return 0;
}
```