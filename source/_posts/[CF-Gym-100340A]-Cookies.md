---
title: '[CF-Gym-100340A]-Cookies'
date: 2018-05-01 14:45:40
tags:
    - 线性DP
mathjax: true
---

[传送门](https://vjudge.net/problem/Gym-100340A)

题意：圣诞老人要将 $m$ 个饼干全部分给 $n$ 个孩子，每个孩子有一个贪婪度，第 $i$ 个孩子的贪婪度为 $g[i]$。如果有 $a[i]$ 个孩子拿到的饼干树比第 $i$ 个孩子多，那么第 $i$ 个孩子会产生 $a[i]g[i]$ 的怨气。$1 \leq n \leq 30$, $n \leq m \leq 5000$.

如果要计算第 $i$ 个孩子的怨气值，就必须要知道 $a[i]$。一个孩子的怨气大小与其他孩子获得的饼干数有关。

仔细思考可以发现，贪婪度大的孩子应该获得更多的饼干。正如「国王游戏」与「皇后游戏」一般，我们可以用邻项交换的方式来证明。因此，可以将孩子按贪婪度降序排列。

设 $f[i][j]$ 为前 $i$ 个孩子分配 $j$ 块饼干时，怨气总和的最小值。有两种情况：
1. 第 $i + 1$ 个孩子获得的饼干数比第 $i$ 个孩子少，此时 $a[i + 1] = i$
2. 第 $i + 1$ 个孩子获得的饼干数与第 $i$ 个孩子相同，此时还需要知道 $i$ 前面有几个孩子与i获得的饼干数相同，才能算出 $a[i + 1]$。

既要知道第 $i$ 个孩子获得的饼干数，又要知道 $i$ 前面有几个孩子与 $i$ 获得的饼干数相同，很难高效地维护 dp。

观察 dp，可发现“每个孩子至少分到一块饼干”。
1. 若第 $i$ 个孩子的饼干数大于$1$，$f[i][j] = f[i][j - i]$（即每个孩子少拿一块饼干）。
2. 若第 $i$ 个孩子的饼干数等于$1$，则枚举i前面有多少个孩子也获得了一块饼干。

状态转移方程如下：

$$f[i][j]=min(f[i][j-i], min(f[k, j - (i - k)] + k * {\sum_{p = k + 1}^{i} g[p]}))$$

初始：$f[0][0] = 0$， 目标：$f[n][m]$。

方案转移，就像「I-country」一样，开两个数组记录“转移来源”，一次递归回去，找到最优路径。

CODE:
``` c++
#include <cstdio>
#include <cmath>
#include <algorithm>
#include <cstring>
using namespace std;

const int N = 35, M = 5005;
int n, m, pre[N], f[N][M], res[N];
int lstN[N][M], lstM[N][M];  // 记录方案

struct node {
    int sum, id;
}g[N];

bool cmp(node a, node b) {return a.sum > b.sum;}

void record(int x, int y) {
    if (!x || !y) return;
    int L = lstN[x][y], R = lstM[x][y];
    if (x == L) {
        for (int i = 1; i <= x; i++) res[g[i].id]++;
        record(L, R);
    } else {
        for (int i = L + 1; i <= x; i++) res[g[i].id]++;
        record(L, R);
    }
}

int main() {
    freopen("cookies.in", "r", stdin);
    freopen("cookies.out", "w", stdout);

    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n; i++) {
        scanf("%d", &g[i].sum);
        g[i].id = i;
    }
    sort(g + 1, g + n + 1, cmp);
    for (int i = 1; i <= n; i++) pre[i] = pre[i - 1] + g[i].sum;
    memset(f, 0x3f, sizeof(f));
    f[0][0] = 0;
    for (int i = 1; i <= n; i++)
        for (int j = i; j <= m; j++) {
            f[i][j] = min(f[i][j], f[i][j - i]);
            lstN[i][j] = i, lstM[i][j] = j - i;
            for (int k = 0; k < i; k++) {
                int sum = f[k][j - (i - k)] + k * (pre[i] - pre[k]);
                if (f[i][j] > sum) {
                    f[i][j] = sum;
                    lstN[i][j] = k, lstM[i][j] = (j - (i - k));
                }
            }
        }
    printf("%d\n", f[n][m]);
    record(n, m);
    for (int i = 1; i <= n; i++) printf("%d ", res[i]);
    putchar('\n');
    return 0;
}
```

真是一道好题。在数据无序的情况下，我们不知道怎么DP，但可以通过额外的算法（本题中是邻项交换）来确定DP状态的计算顺序，还可以在状态空间中运用等效手法来对状态进行缩放。
本题中，我们利用贪心策略，利用相对大小的不变性，将第i + 1个孩子的饼干数先缩放到1，使问题得到简化，容易维护、转移。