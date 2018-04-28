---
title: '[SPOJ]-Mobile Service'
date: 2018-04-28 18:40:40
tags:
---

http://www.spoj.com/problems/SERVICE/

SP703是一道线性动态规划题目，涉及到有趣的思想，不是很难但十分实用，所以在这记录一下。
题意：一个公司有三个移动服务员，初始时分别位于1、2、3处。如果某个位置有一个请求，公司必须派三者之一去那儿，某一时刻只有一名员工能移动，且两名员工不能同时位于同一位置上。从p到q移动员工，需要花费c(p, q)(给定的)。
有n个请求，分别为p1、p2、p3、... pn。公司必须按照顺序依次满足所有请求，目标是最小化公司花费。
N <= 1000, 位置(L)是1~200的整数。

因为员工只有三个，应该很容易想到f[i][x][y][z]，表示解决了前i个请求后，三个员工分别位于x、y、z的最小花费。
该算法规模在8000000000左右，所以还得优化掉一维。

可以发现，每一个请求完成后，必定有一名员工在pi位置上，所以只需要表示另两位员工的位置就好了。
最终，f[i][x][y]表示解决了前i个请求，其中一个位于pi，另外两个分别位于x和y的最小花费。
转移(应该很容易了吧？)：
    f[i + 1][x][y] = min(f[i + 1][x][y], f[i][x][y] + c[p[i]][p[i + 1]]);
    f[i + 1][p[i]][y] = min(f[i + 1][p[i]][y], f[i][x][y] + c[x][p[i + 1]]);
    f[i + 1][x][p[i]] = min(f[i + 1][x][p[i]], f[i][x][y] + c[y][p[i + 1]]);
因为无形中题目附带了信息，所以阶段i和另两位员工的位置就可表明状态。

CODE：
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 205, M = 1005, INF = 1000000000;
int T, L, n;
int c[N][N], p[M], f[M][N][N];

int main() {
    scanf("%d", &T);
    while (T--) {
        scanf("%d%d", &L, &n);
        for (int i = 1; i <= L; i++)
            for (int j = 1; j <= L; j++)
                scanf("%d", &c[i][j]);
        for (int i = 1; i <= n; i++) scanf("%d", &p[i]);
        memset(f, 0x3f, sizeof(f));
        f[0][1][2] = 0, p[0] = 3;
        int ans = INF;
        for (int i = 0; i <= n; i++) {
            for (int x = 1; x <= L; x++) {
                for (int y = 1; y <= L; y++) {
                    if (x == p[i] || y == p[i] || x == y) continue;
                    f[i + 1][x][y] = min(f[i + 1][x][y], f[i][x][y] + c[p[i]][p[i + 1]]);
                    f[i + 1][p[i]][y] = min(f[i + 1][p[i]][y], f[i][x][y] + c[x][p[i + 1]]);
                    f[i + 1][x][p[i]] = min(f[i + 1][x][p[i]], f[i][x][y] + c[y][p[i + 1]]);
                    if (i + 1 == n) ans = min(ans, min(f[i + 1][x][y], min(f[i + 1][p[i]][y], f[i + 1][x][p[i]])));
                }
            }
        }
        printf("%d\n", ans);
    }
    return 0;
}
```

求解线性DP问题，一般先确定“阶段”。若“阶段”不足以表示一个状态，则可以把所需的附加信息也作为状态的维度。
当然是用尽量少的维度来介绍（覆盖）整个状态空间啦，可以排除冗余维度。总结来说，这一题优化的小技巧是，若DP状态由多个维度组成，则应检查这些维度之间能否互相导出。说来容易做来难，还要多练习。