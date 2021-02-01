---
title: '[POJ1037]-A Decorative Fence'
date: 2018-08-14 14:50:40
tags: 
    - 计数DP
mathjax: true
hidden: true
---

[题目链接](https://vjudge.net/problem/POJ-1037)

题意：有 N 块长方形的木板，长度分别为 1 ～ N，宽度都是 1，栅栏都是高低交错的，我们称“两侧比他低的木板”处于高位，“两侧比他高的木板”处于低位。现在给定整数 C，求按照字典序排名为 C 的栅栏中，各木板从左往右依次是多少。$1 \leq N \leq 20, 0 < C < 2^{63}$.

根据“拼凑”思想，我们可以采用“试填法”。

我们可以这样来描述一个状态：F[i, j, k] 表示用 i 块长度不同的木板构成栅栏，最左边的木板长度从小到大排在第 j 位，且位置状态为 k（k = 0 代表低位，k = 1 代表高位）。

预处理每一个 F[i, j, k]，其中方程为：

$$F[i, j, 0] = \sum\limits_{p = j}^{i - 1}F[i - 1, p, 1]$$

$$F[i, j, 1] = \sum\limits_{p = 1}^{j - 1}F[i - 1, p, 0]$$

注意，以下两种表达是等价的（很像离散化）：

* 用长度为 1 ～ i 的木板构成栅栏，其中最左边的长度为 j。
* 用 i 块长度不同的木板构成栅栏，其中最左边的长度排在第 j 位。

在用动态规划预处理后，就可以开始试填了。

动态规划 $O(N^3)$, 试填 $O(N^2)$。

code:
``` c++
#include <cstdio>
#include <cstring>
using namespace std;

typedef long long ll;
int T, n;
ll m, f[25][25][2];

void prework() {
    f[1][1][0] = f[1][1][1] = 1;
    for (int i = 2; i <= 20; i++) {
        for (int j = 1; j <= i; j++) {
            for (int p = j; p <= i - 1; p++)
                f[i][j][0] += f[i - 1][p][1];
            for (int p = 1; p <= j - 1; p++)
                f[i][j][1] += f[i - 1][p][0];
        }
    }
}

int main() {
    prework();
    scanf("%d", &T);
    while (T--) {
        scanf("%d%lld", &n, &m);
        bool used[25];
        memset(used, 0, sizeof(used));
        int last, k;
        for (int j = 1; j <= n; j++) {
            if (f[n][j][1] >= m) {
                last = j, k = 1;
                break;
            } else m -= f[n][j][1];
            if (f[n][j][0] >= m) {
                last = j, k = 0;
                break;
            } else m -= f[n][j][0];
        }
        used[last] = 1;
        printf("%d ", last);
        for (int i = 2; i <= n; i++) {
            k ^= 1;
            int j = 0;
            for (int len = 1; len <= n; len++) {
                if (used[len]) continue;
                j++;
                if (k == 0 && len < last || k == 1 && len > last) {
                    if (f[n - i + 1][j][k] >= m) {
                        last = len; break;
                    } else m -= f[n - i + 1][j][k];
                }
            }
            used[last] = 1;
            printf("%d ", last);
        }
        printf("\n");
    }
    return 0;
}
```