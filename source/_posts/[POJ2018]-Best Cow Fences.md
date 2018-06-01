---
title: '[POJ2018]-Best Cow Fences'
date: 2018-05-09 22:41:40
tags:
    - 二分
---

https://vjudge.net/problem/POJ-2018

题意：给定一个正整数数列A，求一个平均数最大的、长度不小于 L 的子段。

二分判定，判定‘是否存在一个平均数不小于二分的值（mid）、长度不小于 L 的子段’。
平均数还要涉及到小数部分，实在是不方便，那么我们将 A 中的每个元素减去 mid（二分的值），最后只要判定‘是否存在一个子段和非负、长度不小于 L 的子段’。
求解‘子段和非负’这个问题很简单，只要 O(n) 扫描，在子段和为负数时清空即可，扫描过程中的最大子段和即为所求。
考虑到子段和可以用前缀和优化，我们用 sum[i] 表示 A1 ~ Ai 的和。
枚举 i，表示子段和的尾端位置，min_val 表示前 i 个中最小的前缀子段，那么当前的最大子段和即为 max{sum[i] - min_val}。随着 i 的增长，每次只会有一个新的取值进入 min_val 的集合，所以只要设置这一个变量就好了。
还有一个问题就是实数域的二分判定，这时候我们设一个较小的标准 eps， 当 r - l <= eps 时不跳出循环。有的实数域判定是循环100次，那样的精度往往比 eps 更高。

CODE：
``` c++
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <iostream>
using namespace std;

const int N = 100005;
int n, L;
double a[N], b[N], sum[N];

int main() {
    scanf("%d%d", &n, &L);
    for (int i = 1; i <= n; i++) scanf("%lf", &a[i]);
    double l = -1e6, r = 1e6, eps = 1e-5;
    while (r - l > eps) {
        double mid = (l + r) / 2;
        for (int i = 1; i <= n; i++) b[i] = a[i] - mid;
        for (int i = 1; i <= n; i++) sum[i] = sum[i - 1] + b[i];
        double ans = -1e10, min_val = 1e10;
        for (int i = L; i <= n; i++) {
            min_val = min(min_val, sum[i - L]);
            ans = max(ans, sum[i] - min_val);
        }
        if (ans >= 0) l = mid;
        else r = mid;
    }
    cout << int(r * 1000) << endl;
    return 0;
}
```

虽然这道题主考二分，但还有一些二分以外可以掌握的东西。比如说，min_val 前缀记录最小值。前缀最小值是一个很好的避免局部最优解、整体不是最优解的方法。