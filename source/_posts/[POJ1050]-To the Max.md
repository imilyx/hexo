---
title: '[POJ1050]-To the Max'
date: 2018-08-22 14:50:40
tags: 
    - 贪心
mathjax: true
hidden: true
---

[题目链接](https://vjudge.net/problem/POJ-1050)

题意：寻找一个 $n * n$ 的矩阵的最大子矩阵和。

n <= 100, 因此 $n^3$ 就能过。$n^3$ 的做法是枚举行上下界，对于每一列用类似最大子序列和的方法计算。
为了快速算出某一列上下界之间数字的和，可以用前缀和优化。

code:
``` c++
#include <cstdio>
#include <algorithm>
using namespace std;

int n, a[105][105], sum[105][105], dp[105], ans;

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= n; j++) {
            scanf("%d", &a[i][j]);
            sum[i][j] = sum[i - 1][j] + a[i][j];
        }
    for (int i = 1; i <= n; i++) {
        for (int j = i; j <= n; j++) {
            dp[1] = sum[j][1] - sum[i - 1][1];
            ans = max(ans, dp[1]);
            for (int k = 2; k <= n; k++) {
                dp[k] = sum[j][k] - sum[i - 1][k];
                if (dp[k - 1] > 0) dp[k] += dp[k - 1];
                ans = max(ans, dp[k]);
            }
        }
    }
    printf("%d\n", ans);
    return 0;
}
```