---
title: '[BZOJ1398]-Necklace'
date: 2018-08-23 16:35:40
tags: 
    - 最小表示法
mathjax: true
---

[题目链接](https://vjudge.net/problem/HYSBZ-1398)

题意略。

分别计算出两个字符串的最小表示法，如果相等，输出 "Yes" 并输出最小表示法，否则输出 "No".

与写两个函数分别用来判断是否相等和计算最小表示法相比，这个做法是很方便的，而且也减少了错误率。

code:
``` c++
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <iostream>
using namespace std;

const int N = 1e6 + 10;
int n, ans;
char s1[N << 1], s2[N << 1];

bool equal(char a[], char b[]) {
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            bool eq = 1;
            for (int k = 0; k < n; k++)
                if (a[(i + k) % n] != b[(j + k) % n]) eq = 0;
            if (eq) return 1;
        }
    }
    return 0;
}

int solve(char a[]) {
    int i = 1, j = 2, k;
    while (i <= n && j <= n) {
        for (k = 0; k <= n && a[i + k] == a[j + k]; k++);
        if (k == n) break;
        if (a[i + k] > a[j + k]) {
            i = i + k + 1;
            if (i == j) i++;
        } else {
            j = j + k + 1;
            if (i == j) j++;
        }
    }
    return min(i, j);
}

int main() {
    scanf("%s", s1);
    scanf("%s", s2);
    n = strlen(s1);
    for (int i = n; i >= 1; i--) {
        s1[i] = s1[i - 1], s1[i + n] = s1[i];
        s2[i] = s2[i - 1], s2[i + n] = s2[i];
    }
    int ans1 = solve(s1), ans2 = solve(s2);
    bool eq = 1;
    for (int i = 0; i < n; i++) if (s1[ans1 + i] != s2[ans2 + i]) {
        eq = 0; break;
    }
    if (eq) {
        printf("Yes\n");
        for (int i = 0; i < n; i++) printf("%c", s1[ans1 + i]);
        puts("");
    } else printf("No\n");
    return 0;
}
```