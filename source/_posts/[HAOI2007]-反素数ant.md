---
title: '[HAOI2007]-反素数ant'
date: 2018-06-16 22:10:40
tags: 
    - 数学
mathjax: true
hidden: true
---

[传送门](https://www.lydsy.com/JudgeOnline/problem.php?id=1053)

题意：对于任何正整数 x，其约数的个数记作 $g(x)$ 。例如 $g(1)=1$ ，$g(6)=4$ 。如果某个正整数x满足：$g(x) > g(i)$ $0 < i < x$ ，则称 x 为反质数。例如，整数 1，2，4，6 等都是反质数。现在给定一个数 N ，你能求出不超过 N 的最大的反质数么？

引理1: 1～N 中最大的反质数，就是 1～N 中约数个数最多的数中最小的一个。

证明：应该很容易理解，掠过。

引理2: 1～N 中任何数的不同质因子都不会超过10个，且所有质因子的指数总和不超过30。

证明：最小的11个质数(2~31) 乘积 > 2000000000 ，且即使只包含最小的质因子2，仍然有 2^31 > 2000000000 。

引理3: $x \in [1, N]$ ，x 为反质数的必要条件是 x 的质因子是连续的若干个最小的质数，并且指数单调递减。

证明：反证法。若 x 的质因数分解式中存在一项 $p^k (p > 29)$ ，则必定有一个不超过29的质因子 p' 不能整除 x。根据算术基本定理的推论，$x / p^k * p'^k$ 的约数与 x 的约数个数相等，但前者更小，这与反质数的定义矛盾。同理，若 x 的质因子不是不下降或单调递减的，我们也可以通过上述交换质因子的方法，得到一个比 x 更小、但约数个数相等的整数。故原命题成立。

综上所述，我们可以使用深度优先搜索尝试确定前10个质数的指数，并满足指数单调递减或连续不减，总乘积不超过 N，同时记录约数的个数。
通过引理二可以说明搜索量非常小，每当搜出一个满足条件的整数时，我们就按引理一的结论更新答案。

CODE：
``` c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
const int prime[15] = {1, 2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37};
ll n, Maxn, ans, s[105];

void dfs(int x, ll index, ll sum) {  // x 表示搜到第几个质因子了，index 记录指数之和，sum 记录总乘积
    if (x > 12) return;
    if (index > Maxn || (index == Maxn && sum < ans)) {
        Maxn = index, ans = sum;
    }
    s[x] = 0;
    while (sum * prime[x] <= n && s[x] < s[x - 1]) {
        s[x]++;
        sum *= prime[x];
        ll next = index * (s[x] + 1);
        dfs(x + 1, next, sum);
    }
}

int main() {
    scanf("%lld", &n);
    s[0] = 100000;
    dfs(1, 1, 1);
    printf("%lld\n", ans);
    return 0;
}
```