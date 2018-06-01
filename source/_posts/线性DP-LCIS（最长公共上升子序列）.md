---
title: 线性DP-LCIS（最长公共上升子序列）
date: 2018-04-10 10:34:40
tags:
    - 线性动规
---

我们一定很熟悉LIS（最长上升子序列）和LCS（最长公共子序列）
那么来看看这道，LCIS--最长公共上升子序列：
输入a、b两序列，求出它们的LCIS；

初步思想可以是：f[i][j]表示a序列前i个与b序列前i个（其中b[j]为结尾元素）的LCIS的长度；
a[i] != b[j]时， f[i][j] = for i from 0 to j - 1  b[k] < b[j] max{f[i - 1][k] + 1};
可以用三重循环实现，但我们还可以进行优化；

对于a[i] = b[j], f[i][j]可以选择的所有f[i - 1][k]都属于它的决策集合（from lyd）
所以记val为f[i][j]决策集合中最长的一个，
这样每次经过一个符合要求的f[i - 1][k]时，val都可以记录一下，
注意，是顺带的；

但是我们又发现了·^`f[i][]都是由f[i - 1][]转移来的，所以
最优解法只有一维：f[i]表示a序列前i位与b序列的LCIS；

CODE：
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 505;
int n, m, a[N], b[N], f[N];

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d", &a[i]);
    scanf("%d", &m);
    for (int i = 1; i <= m; i++) scanf("%d", &b[i]);
    for (int i = 1, t = 0; i <= n; i++, t = 0) {
        for (int j = 1; j <= m; j++) {
            if (a[i] == b[j])
                f[j] = val + 1;
            if (b[j] < a[i] && f[t] < f[j])
                val = f[j];
        }
    }
    int p = 0;
    for (int i = 1; i <= m; i++)
        if (f[i] > f[p]) p = i;
    printf("%d\n", f[p]);
    return 0;
}
```

觉得自己DP很弱啊。。其实有很多类似val这样的决策优化，
对于“决策集合中的元素只增多不减少”的情况，就可以维护一个变量去记录信息，
所以DP重要的是要去注意用来转移的东西的变化，
甚至有的就不计算出一个状态，而是考虑“一个已知状态应该更新那些后续阶段的未知状态”，
真的很妙啊...