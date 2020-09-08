---
title: '[COCI2010]-ZUMA'
date: 2020-09-08 14:46:40
tags: 
    - 区间dp
    - 思维
mathjax: true
---

第一次碰到不会做的区间 dp。。这题的难点在于设计 dp 状态。。

发现普通的思路 $f[i, j]$ 没有办法做

$f[i, j, k]$ 表示在区间 $[i, j]$ 前放了 $k$ 个与 $c[i]$ 同色的球，连带着新加的球全部消掉的最小球数。

$f[i, j, k] =$
1. $f[i, j, k + 1] + 1$（在前面加一个球）
2. $f[i + 1, j, k + 1]$ $(c[i] = c[i + 1])$
3. $f[i + 1, p - 1, 0] + f[p, j, k + 1]$ $(c[i] = c[p])$

边界：$f[i, i, j] = K - 1 - j$

$f[i, j, K - 1] =$
1. $f[i + 1, j, 0]$
2. $f[i + 1, j, K - 1]$ $(c[i] = c[i + 1])$（因为要求 $\geq K$ 个连续的球）
3. $f[i + 1, p - 1, 0] + f[p, j, K - 1]$（理由同 2）

code:
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 105, KK = 10;
int n, K, c[N], f[N][N][KK];

void chkmin(int &x, int y) { x = min(x, y); }

int main() {
    cin >> n >> K;
    memset(f, 0x3f, sizeof(f));
    rep(i, 1, n) {
        cin >> c[i];
        rep(j, 0, K - 1) f[i][i][j] = K - 1 - j;
    }
    rep(len, 2, n) {
        rep(i, 1, n - len + 1) {
            int j = i + len - 1;

            chkmin(f[i][j][K - 1], f[i + 1][j][0]);
            if (c[i] == c[i + 1])
                chkmin(f[i][j][K - 1], f[i + 1][j][K - 1]);
            rep(p, i + 2, j)
                if (c[i] == c[p])
                    chkmin(f[i][j][K - 1], f[i + 1][p - 1][0] + f[p][j][K - 1]);
            
            for (int k = K - 2; k >= 0; k--) {
                chkmin(f[i][j][k], f[i][j][k + 1] + 1);
                if (c[i] == c[i + 1])
                    chkmin(f[i][j][k], f[i + 1][j][k + 1]);
                rep(p, i + 2, j)
                    if (c[i] == c[p])
                        chkmin(f[i][j][k], f[i + 1][p - 1][0] + f[p][j][k + 1]);
            }
        }
    }
    printf("%d\n", f[1][n][0]);
    return 0;
}
```