---
title: '[POJ1964]-City Game'
date: 2018-08-23 16:12:40
tags:
    - 单调栈
mathjax: true
---

[题目链接](https://vjudge.net/problem/POJ-1964)

题意：给定一个 $n * m$ 的方格图，其中元素为 F 或 R，求最大的 F 矩阵（答案乘以3再输出）。

因为 $1 \leq n, m \leq 1000$, 只能 $n^2$ 过。可以使用单调栈。

即记 s[i, j] 为第 j 列从上往下以第 i 行的元素为结尾的最长 F 串长度。

枚举行，对于第 i 行存一个单调递增的栈，若 s[i, now] 小于当前栈的末尾元素，就不断弹出并计算答案。

code:
``` c++
#include <cstdio>
#include <iostream>
#include <cstring>
#include <algorithm>
using namespace std;

const int N = 1010;
int T, n, m, a[N][N], s[N][N], f[N], w[N], ans;

void solve() {
    memset(s, 0, sizeof(s));
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++)
            s[i][j] = a[i][j] ? s[i - 1][j] + 1 : 0;
    for (int i = 1; i <= n; i++) s[i][0] = s[i][m + 1] = 0;
    int p = 1;
    for (int i = 1; i <= n; i++) {
        f[p = 1] = -1, w[p] = 0;
        for (int j = 1; j <= m + 1; j++) {
            if (s[i][j] > f[p]) {
                f[++p] = s[i][j], w[p] = 1;
            } else {
                int width = 0;
                while (f[p] > s[i][j]) {
                    width += w[p];
                    ans = max(ans, width * f[p]);
                    p--;
                }
                f[++p] = s[i][j], w[p] = width + 1;
            }
        }
    }
}

int main() {
    scanf("%d", &T);
    while (T--) {
        scanf("%d%d", &n, &m);
        memset(a, 0, sizeof(a));
        for (int i = 1; i <= n; i++)
            for (int j = 1; j <= m; j++) {
                char ch;
                cin >> ch;
                if (ch == 'F') a[i][j] = 1;
            }
        ans = 0;
        solve();
        printf("%d\n", ans * 3);
    }
    return 0;
}
```