---
title: '[POJ2185]-Milking Grid'
date: 2018-08-23 16:40:40
tags:
    - KMP
mathjax: true
---

[题目链接](https://vjudge.net/problem/POJ-2185)

题意：找出一个 $r * c$ 字符矩阵的最小子矩阵，使得这个子矩阵无穷倍复制后包含原矩阵。

首先要知道，最小子矩阵一定可以出现在 $r * c$ 字符矩阵的左上角处。

根据 POJ1961: Period 一题我们已经得到了结论，若字符串 S[1 ~ n] 存在最小循环元，则最小循环元的长度必定为 n - next[n]，与本题不同的是，1961 要求 n % (n - next[n]) = 0，即完全包含，但本题不用，所以我们只要用 KMP 匹配 next[]，然后对行和列分别处理。

以行为例，将每一行看作一个元素，对第 i 行(1 <= i <= r) 计算出 next[i], 即前 i 行就像一个序列一样。对列操作也类似。这样就可以求出行的最小循环元长度和列的最小循环元长度，相乘并输出。

code:
``` c++
#include <cstdio>
#include <iostream>
using namespace std;

int R, C, nxt[10010];
char s[10010][80];

bool cmp_col(int y1, int y2) {
    for (int x = 1; x <= R; x++)
        if (s[x][y1] != s[x][y2]) return false;
    return true;
}

int calc_col() {
    nxt[1] = 0;
    for (int i = 2, j = 0; i <= C; i++) {
        while (j > 0 && !cmp_col(j + 1, i)) j = nxt[j];
        if (cmp_col(j + 1, i)) j++;
        nxt[i] = j;
    }
    return C - nxt[C];
}

bool cmp_row(int x1, int x2) {
    for (int y = 1; y <= C; y++)
        if (s[x1][y] != s[x2][y]) return false;
    return true;
}

int calc_row() {
    nxt[1] = 0;
    for (int i = 2, j = 0; i <= R; i++) {
        while (j > 0 && !cmp_row(j + 1, i)) j = nxt[j];
        if (cmp_row(j + 1, i)) j++;
        nxt[i] = j;
    }
    return R - nxt[R];
}

int main() {
    while (~scanf("%d%d", &R, &C)) {
        for (int i = 1; i <= R; i++)
            for (int j = 1; j <= C; j++)
                cin >> s[i][j];
        printf("%d\n", calc_row() * calc_col());
    }
    return 0;
}
```