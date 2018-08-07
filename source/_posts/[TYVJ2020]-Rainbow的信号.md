---
title: '[TYVJ2020]-Rainbow的信号'
date: 2018-08-07 23:11:11
tags:
    - 概率与期望
mathjax: true
---

http://www.joyoi.cn/problem/tyvj-2020

题意略。

首先要明确的是，位运算是不进位的，各位之间互不影响，因此我们可以分位讨论。设 B 是一个01序列，B[i] 等于 A[i] 的第 k 位。

按照题目中 l，r 的选取方式，长度为 1 的区间(l = r)，概率为 $1 / N^2$，其他区间则是 $2 / N^2$。我们先 O(n) 检查序列 B 中的每个数是否为 1(长度为 1 的区间), 若是，则累加 $2^k * 1 / N^2$ 到答案中。接下来就枚举区间长度大于 1 的。

其余没什么好说的，具体看代码。时间复杂度 $O(N\ log\ MAX\_INT)$。

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 1e5 + 10;
int n, cnt[2], last[2], a[N], b[N];  // last[k] 表示 k 上一次出现的位置(k = 0 or 1)
double ans_or = 0, ans_xor = 0, ans_and = 0;

void calc(int x) {
    memset(cnt, 0, sizeof(cnt));
    memset(last, 0, sizeof(last));
    for (int i = 1; i <= n; i++) b[i] = ((a[i] >> x) & 1);
    for (int i = 1; i <= n; i++)
        if (b[i]) {
            double tmp = (1 << x) * 1.0 / n / n;
            ans_xor += tmp, ans_or += tmp, ans_and += tmp;
        }
    for (int i = 1; i <= n; i++) {
        if (i > 1) {
            ans_xor += (1 << x) * 2.0 / n / n * cnt[!b[i]];
            if (!b[i]) ans_or += (1 << x) * 2.0 / n / n * last[1];
            else {
                ans_or += (1 << x) * 2.0 / n / n * (i - 1);
                ans_and += (1 << x) * 2.0 / n / n * (i - 1 - last[0]);
            }
        }
        last[b[i]] = i;
        if (!b[i]) cnt[0]++;
        else swap(cnt[0], cnt[1]), cnt[1]++;
    }
}

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d", &a[i]);
    for (int i = 0; i <= 30; i++) calc(i);
    printf("%.3lf %.3lf %.3lf\n", ans_xor, ans_and, ans_or);
    return 0;
}
```