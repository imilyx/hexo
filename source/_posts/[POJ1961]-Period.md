---
title: '[POJ1961]-Period'
date: 2018-08-22 20:42:40
tags:
    - KMP
hidden: true
---

[题目链接](https://vjudge.net/problem/POJ-1961)

题意：给定一个长度为 N 的字符串 S，对 S 的每一个前缀，如果它的最大循环次数大于1，则输出该前缀的最小循环元长度和最大循环次数。

首先我们要了解，对于一个前缀 S[1 ~ i], i - next[i] 就是最小循环元。

可以这么理解，我们假设到达位置 i - 1 时，字符串一直循环完整，那么到第 i 位时失配了。根据 KMP 算法的进行，我们要进行回溯。

现在循环节的末尾是 i - 1，上一个循环节的末尾是 next[i] - 1, 那么因为 next[i] 是最大的，循环节的长度--即 (i - 1) - (next[i] - 1) = i - next[i] 也就是最小的了。

同时，如果 i % (i - next[i]) = 0, 总字符数是循环节长度倍数，那么这个循环就是答案。

需要注意，next[i] < i.

同理，我们可以推出，如果 i - next[next[i]] 能整除 i，那么 S[1 ~ i - next[next[i]]] 就是 S[1 ~ i] 的次小循环元。剩下依次类推。

code:
``` c++
#include <cstdio>
using namespace std;

const int N = 1e6 + 10;
char a[N];
int nxt[N], n, T;

void calc_next() {
    nxt[1] = 0;
    for (int i = 2, j = 0; i <= n; i++) {
        while (j > 0 && a[i] != a[j + 1]) j = nxt[j];
        if (a[i] == a[j + 1]) j++;
        nxt[i] = j;
    }
}

int main() {
    while (~scanf("%d", &n) && n) {
        scanf("%s", a + 1);
        calc_next();
        printf("Test case #%d\n", ++T);
        for (int i = 2; i <= n; i++) {
            if (i % (i - nxt[i]) == 0 && i / (i - nxt[i]) > 1)
                printf("%d %d\n", i, i / (i - nxt[i]));
        }
        puts("");
    }
    return 0;
}
```