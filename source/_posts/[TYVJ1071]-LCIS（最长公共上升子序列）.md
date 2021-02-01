---
title: '[TYVJ1071]-LCIS（最长公共上升子序列）'
date: 2018-04-10 10:34:40
tags:
    - 线性DP
mathjax: true
hidden: true
---

http://www.joyoi.cn/problem/tyvj-1071

题意略。

这道题目是LIS和LCS的综合。

F[i, j] 表示 $A_1$ ~ $A_i$ 与 $B_1$ ~ $B_j$ 构成的以 $B_j$ 为结尾的 LCIS 的长度。不妨假设 $A_0 = B_0 = -\infty$.

当 $A_i \neq B_j$ 时，

$$F[i, j] = F[i - 1, j]$$

当 $A_i = B_j$ 时，

$$F[i, j] = \max\limits_{0 \leq k < j, B_k < B_j}\{F[i - 1, k]\} + 1 = \max\limits_{0 \leq k < j, B_k < A_i} \{F[i - 1, k]\} + 1$$

但是我们又发现了·^`f[i][]都是由f[i - 1][]转移来的，所以最优解法只有一维：f[i]表示a序列前i位与b序列的LCIS.

CODE：
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 3010;
int f[N], a[N], b[N], n;

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d", &a[i]);
    for (int i = 1; i <= n; i++) scanf("%d", &b[i]);
    int ans = 0;
    for (int i = 1; i <= n; i++) {
        int k = 0;
        for (int j = 1; j <= n; j++) {
            if (a[i] == b[j]) {
                f[j] = max(f[j], f[k] + 1);
                ans = max(ans, f[j]);
            } else if (b[j] < a[i] && f[k] < f[j]) k = j;
        }
    }
    printf("%d\n", ans);
    return 0;
}
```

觉得自己DP很弱啊。。其实有很多类似val这样的决策优化，
对于“决策集合中的元素只增多不减少”的情况，就可以维护一个变量去记录信息，
所以DP重要的是要去注意用来转移的东西的变化，
甚至有的就不计算出一个状态，而是考虑“一个已知状态应该更新那些后续阶段的未知状态”，
真的很妙啊...