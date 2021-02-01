---
title: '[SDOI2009]-HH去散步'
date: 2019-07-24 14:45:40
tags:
	- 矩阵乘法
hidden: true
---

[传送门](https://www.lydsy.com/JudgeOnline/problem.php?id=1875)

t 这么大！矩阵乘法无疑

唯一需要注意的是：HH 不走回头路。。怎么办呢？

我们可以记录边的编号，dp[i, j] 表示从 i 这条边走到 j 这条边。这样在构造转移矩阵的时候，就可以不把 T[i, i ^ 1] 置为 1 了（i 和 i ^ 1 是一对边）

边的编号从 2 开始，我们把不存在的初始边称为 1，那么答案就是 sigma{dp[1, B 点周围边的编号]}.

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int mod = 45989, N = 205;
int n, m, D, A, B, tot;
int to[N << 1], nxt[N << 1], lnk[N], cnt = 1, from[N << 1];

struct Matrix {
    int x[N][N];
}ans, T;

void add(int x, int y) {
    to[++cnt] = y, nxt[cnt] = lnk[x], lnk[x] = cnt, from[cnt] = x;
}

Matrix operator * (Matrix a, Matrix b) {
    Matrix c;
    memset(c.x, 0, sizeof(c.x));
    rep(i, 1, cnt) {
        rep(j, 1, cnt) {
            rep(k, 1, cnt) {
                c.x[i][j] = (c.x[i][j] + a.x[i][k] * b.x[k][j]) % mod;
            }
        }
    }
    return c;
}

Matrix quick_pow(Matrix a, int b) {
    Matrix ret;
    memset(ret.x, 0, sizeof(ret.x));
    rep(i, 1, cnt) ret.x[i][i] = 1;
    for (; b; b >>= 1) {
        if (b & 1) ret = ret * a;
        a = a * a;
    }
    return ret;
}

int main() {
    scanf("%d%d%d%d%d", &n, &m, &D, &A, &B);
    rep(i, 1, m) {
        int a, b; scanf("%d%d", &a, &b);
        add(a, b), add(b, a);
    }
    for (int i = 2; i <= cnt; i++)
        for (int j = 2; j <= cnt; j++)
            if (i != (j ^ 1) && to[i] == from[j])
                T.x[i][j] = 1;
    for (int i = lnk[A]; i; i = nxt[i]) ans.x[1][i] = 1;
    ans = ans * quick_pow(T, D - 1);
    for (int i = lnk[B]; i; i = nxt[i]) tot = (tot + ans.x[1][i ^ 1]) % mod;
    printf("%d\n", tot);
    return 0;
}
```