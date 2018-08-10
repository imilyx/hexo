---
title: '[SGU167]-I-country'
date: 2018-04-30 15:57:40
tags:
    - 线性DP
---

https://vjudge.net/problem/SGU-167

题意：在一个n行m列的矩阵中，每个格子有一个权值，要求寻找一个包含k个格子的凸连通块。n, m <= 15。如：
``` c++
0 0 0 0 0 0 0 0
0 0 0 1 0 0 0 0
0 0 1 1 1 0 0 0
0 0 1 1 1 1 0 0
0 0 0 1 1 1 0 0
0 0 0 1 1 0 0 0
0 0 0 0 0 0 0 0
```
（上图中为1的格子表示被选）


这是一道线性DP。
初看时毫无头绪，仔细想想可以发现，既然是个凸的图形，它的左右两边坐标必定分别为先减后增和先增后减。那么就可以抓住这一点来设计状态表示。
再考虑到n和m为0～15的整数，可以设f[i][j][l][r][x][y]表示前i行选了j个格子，第i行选了第l到r个格子（不选则表示为0），左边界单调性为x，右边界单调性为y（0表示坐标递增，1表示坐标递减）时的最大权值和，符合“阶段线性增长”的特点。

需要注意的是，若左边界单调性为0，则表示左边界收缩，若右边界单调性为0，则表示右边界扩张。
若一个边界开始收缩了，那么转移到这步时就要转移两种方案，因为一种是一直收缩，一种是处在转折点，刚开始收缩。
若两个边界都收缩，那就有四种方案要转移啦。
若两个边界都扩张，那只有一种方案可转移（因为是凸的，不会先收缩再扩张，扩张是第一阶段）。
转移时枚举上一行选的区间的左右端点p和q，根据左右边界的扩张和收缩来确定pq范围。

还有个需要注意的(?)，就是表示单调性的x和y，含义是下一行（如果存在）的单调性。
例如第i行左边界单调性为0，第i+1行l必定大于等于第i行的l，也就是第i+1行对于第i行来说左边界是收缩的。

记录方案的话嘛，不难，开四个数组记录最优解是从何处转移而来的，最终通过一次递归，沿着记录的每一步“转移来源”回到初态，即可得到一条最优解的路径，即为方案。这就有一点追根溯源的味道了。

还有。。vjudge上的内存限制为64MB，第一次提交时70MB，炸裂，后来改开short和bool，节省了不少字节，几乎是原来一半的空间。

转移方程程序中都有，就不给了。讲得不甚清楚，具体看程序。

CODE:
``` c++
#include <cstdio>
#include <cmath>
#include <algorithm>
#define rep(i, x, y) for (i = x; i <= y; i++)
using namespace std;

const int N = 16;
int n, m, size, k, now, x, y, i, j, l, r, p, q, ans, ai, al, ar, ax, ay;
int f[N][226][N][N][2][2], a[N][N], pre[N][N];
short rangeL[N][226][N][N][2][2], rangeR[N][226][N][N][2][2];
bool dirL[N][226][N][N][2][2], dirR[N][226][N][N][2][2];

void update(int data, int L, int R, int X, int Y) {
    if (data < f[i][j][l][r][x][y]) return;
    f[i][j][l][r][x][y] = data;
    rangeL[i][j][l][r][x][y] = L, rangeR[i][j][l][r][x][y] = R;
    dirL[i][j][l][r][x][y] = X, dirR[i][j][l][r][x][y] = Y;
}

void print(int I, int J, int L, int R, int X, int Y) {
    if (!J) return;
    print(I - 1, J - (R - L + 1), rangeL[I][J][L][R][X][Y], rangeR[I][J][L][R][X][Y], dirL[I][J][L][R][X][Y], dirR[I][J][L][R][X][Y]);
    for (int v = L; v <= R; v++) printf("%d %d\n", I, v);
}

int main() {
    scanf("%d%d%d", &n, &m, &size);
    rep(i, 1, n)
        rep(j, 1, m) {
            scanf("%d", &a[i][j]);
            pre[i][j] = pre[i][j - 1] + a[i][j];
        }
    rep(i, 1, n) f[i][0][0][0][1][0] = 0;
    rep(i, 1, n) rep(j, 1, size) rep(l, 1, m) rep(r, l, m) {
                    if ((k = r - l + 1) > j) break;
                    now = pre[i][r] - pre[i][l - 1];
                    x = 1, y = 1;
                    rep(p, l, r) rep(q, r, m) {
                        update(f[i - 1][j - k][p][q][1][1] + now, p, q, 1, 1);
                        update(f[i - 1][j - k][p][q][1][0] + now, p, q, 1, 0);
                    }
                    x = 0, y = 0;
                    rep(p, 1, l) rep(q, l, r) {
                        update(f[i - 1][j - k][p][q][0][0] + now, p, q, 0, 0);
                        update(f[i - 1][j - k][p][q][1][0] + now, p, q, 1, 0);
                    }
                    x = 1, y = 0;
                    if (j == k)
                        update(f[i - 1][0][0][0][1][0] + now, 0, 0, 1, 0);
                    else
                        rep(p, l, r) rep(q, p, r) update(f[i - 1][j - k][p][q][1][0] + now, p, q, 1, 0);
                    x = 0, y = 1;
                    rep(p, 1, l) rep(q, r, m) {
                        update(f[i - 1][j - k][p][q][0][0] + now, p, q, 0, 0);
                        update(f[i - 1][j - k][p][q][1][0] + now, p, q, 1, 0);
                        update(f[i - 1][j - k][p][q][0][1] + now, p, q, 0, 1);
                        update(f[i - 1][j - k][p][q][1][1] + now, p, q, 1, 1);
                    }
                }
    rep(i, 1, n) rep(l, 1, m) rep(r, l, m) rep(x, 0, 1) rep(y, 0, 1) {
        if (ans > f[i][size][l][r][x][y]) continue;
        ans = f[i][size][l][r][x][y];
        ai = i, al = l, ar = r, ax = x, ay = y;
    }
    printf("Oil : %d\n", ans);
    print(ai, size, al, ar, ax, ay);
    return 0;
}
```

是不是特别长？敲起来爽啊！
以前做题时从不考虑空间限制（毕竟题目都很宽容），这回是第一次，以后也得养成估计空间的良好习惯。
几乎每一道DP都要求找性质（除了无脑的），这一道就是凸边形的本质，就是两道边拼起来。难题重点在于找性质。
这时一道不错的DP，对于蒟蒻（如我）代码水平能得到锻炼。看着一份优秀的代码诞生，就好像画了幅非常满意的画。