---
title: '[POJ1737]-Connected Graph'
date: 2018-08-10 18:50:40
tags: 
    - 计数DP
    - 排列组合
mathjax: true
---

https://vjudge.net/problem/POJ-1737

题意：求 N 个节点的无向连通图有多少个，节点有标号，为 1 ～ N，$1 \leq N \leq 50$.

（诶我记得有一年的初赛问题求解T2是这个！）

当时那道初赛，4 个点，我是先算出了 $C_6^3 + C_6^4 + C_6^5 + C_6^6$，再减去三个点之间有三条边、一个点孤立的三种情况，共是38，与题例4相同。

那么我们也可以用类似容斥的方法，来做这题。

N 个点的无向图总数是 $2^{N * (N - 1) / 2}$。

接下来计算 N 个点的不连通无向图的数量。一个不连通无向图必定由若干连通块组成。根据 “根据围绕基准点构造一个整体” 的思想，我们可以枚举标号为 1 的节点所在的连通块包含的节点个数 k，从 2 ～ i 这 i - 1 个节点中选出 k - 1 个节点构成任意无向图，有 $2 ^ {(i - k) * (i - k - 1) / 2}$ 种方法。

综上所述，设 F[i] 表示 i 个节点的无向连通图个数：

$$F[i] = 2^{i * (i - 1) / 2} - \sum\limits_{j = 1}^{i - 1} F[j] * C_{i - 1}^{j - 1} * 2^{(i - j) * (i - j - 1) / 2}$$

可是，这题需要高精度，所以目前我还未AC，以上只是此题主要思路！

code:
``` c++
#include <cstdio>
using namespace std;

typedef long long ll;
int n;
ll f[60], C[60][60], sum[60];

ll calc(int x) {return 1ll << (x * (x - 1) / 2);}

int main() {
    f[1] = 1;
    C[0][0] = 1;
    for (int i = 1; i <= 50; i++) {
        C[i][0] = 1;
        for (int j = 1; j <= i; j++) C[i][j] = C[i - 1][j] + C[i - 1][j - 1];
    }
    for (int i = 2; i <= 50; i++) {
        sum[i] = 0;
        for (int j = 1; j < i; j++)
            sum[i] += C[i - 1][j - 1] * f[j] * calc(i - j);
        f[i] = calc(i) - sum[i];
    }
    while (~scanf("%d", &n) && n) {
        printf("%lld\n", f[n]);
    }
    return 0;
}
```

扩展题目：[HDU5729]-Rigid Frameworks

https://vjudge.net/problem/HDU-5729

题意：因为矩形是不稳定的，会变成平行四边形，但是可以在矩形对角线加边，通过构成三角形使这个矩形稳定下来。给一 $n * m$ 的矩形，可以在单位矩形里加两种对角线（从左上到右下，从左下到右上两种），或者不加对角线，或者两条。问使这个 $n * m$ 的矩形稳定下来的方案数。

原问题等价于求左边有 n 个点，右边有 m 个点的连通的二分图的数目，可以用类似连通图计数的方法 dp 得到。

每个单位矩阵可以加两种对角线（从左上到右下，从左下到右上两种），或者不加对角线，共 3 种选择，则一个 $n * m$ 矩阵的总方案数是 $3^{nm}$。

我们设 f[i, j] 表示使 $i * j$ 矩阵固定下来的合法方案数。

与 POJ1737 类似，该题转移方程为：

$$f[n, m] = 3^{n * m} - C_{n - 1}^{i - 1} * C_m^j * f[i, j] * 3^{(n - i) * (m - j)}$$

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
const int N = 40, mod = 1e9 + 7;
ll C[N][N], g[N * N], f[N][N], inv[N * N];

int main() {
    g[0] = 1;
    for (int i = 1; i <= 110; i++) g[i] = g[i - 1] * 3 % mod;
    C[0][0] = 1;
    for (int i = 1; i < N; i++) {
        C[i][0] = C[i][i] = 1;
        for (int j = 1; j < i; j++) C[i][j] = C[i - 1][j] + C[i - 1][j - 1];
    }
    for (int i = 1; i <= 10; i++)
        for (int j = 0; j <= 10; j++) {
            f[i][j] = g[i * j];
            if (i == 1 && j == 0) f[i][j] = 1;
            for (int n = 1; n <= i; n++)
                for (int m = 0; m <= j; m++) {
                    if (i == n && j == m) continue;
                    f[i][j] -= C[i - 1][n - 1] * C[j][m] % mod * f[n][m] % mod * g[(i - n) * (j - m)] % mod;
                    f[i][j] = (f[i][j] % mod + mod) % mod;
                }
        }
    int n, m;
    while (~scanf("%d%d", &n, &m)) printf("%lld\n", f[n][m]);
    return 0;
}
```