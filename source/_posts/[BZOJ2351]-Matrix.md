---
title: '[BZOJ2351]-Matrix'
date: 2018-08-23 16:30:40
tags: 
    - Hash
mathjax: true
hidden: true
---

[题目链接](https://vjudge.net/problem/HYSBZ-2351)

题意略。

矩阵哈希，对于一个位置 [x, y] 我们分别记录它的横向与纵向，记录方法与一般字符串哈希无异。

求出矩阵哈希前缀和，开一个 map 数组 mark，记录在原矩阵中哪些可能的 $a * b$ 的矩阵出现过，对于每一个询问，计算出输入矩阵的哈希值 res，如果 mark[res] > 0, 输出 1，反之输出 0.

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

typedef unsigned long long ull;
const int p1 = 131, p2 = 233, N = 1005;
int n, m, a, b, q;
char s[N];
ull h[N][N], pow1[N], pow2[N];

map<int, int> mark;

int main() {
    scanf("%d%d%d%d", &n, &m, &a, &b);
    for (int i = 1; i <= n; i++) {
        scanf("%s", s + 1);
        for (int j = 1; j <= m; j++)
            h[i][j] = h[i][j - 1] * p1 + (s[j] - '0');
    }
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++)
            h[i][j] += h[i - 1][j] * p2;
    pow1[0] = pow2[0] = 1;
    for (int i = 1; i <= n; i++) pow2[i] = pow2[i - 1] * p2;
    for (int i = 1; i <= m; i++) pow1[i] = pow1[i - 1] * p1;
    for (int i = a; i <= n; i++)
        for (int j = b; j <= m; j++)
            mark[h[i][j] - h[i - a][j] * pow2[a] - h[i][j - b] * pow1[b] + h[i - a][j - b] * pow2[a] * pow1[b]] = 1;
    scanf("%d", &q);
    while (q--) {
        ull res = 0;
        for (int i = 1; i <= a; i++) {
            scanf("%s", s + 1);
            for (int j = 1; j <= b; j++)
                res += (s[j] - '0') * pow2[a - i] * pow1[b - j];
        }
        printf("%d\n", (mark[res] ? 1 : 0));
    }
    return 0;
}
```