---
title: '[POJ1015]-Jury Compromise'
date: 2018-05-19 20:00:40
tags: 
    - 背包
mathjax: true
---

https://vjudge.net/problem/POJ-1015

题意：有 n 个人，第 i 人有两个值 a[i] 和 b[i]，需要其中选出 m 个人，满足 $|\sum_{i=1}^n a_i - \sum_{i=1}^n b_i|$ 最小。
若选择方法不唯一，那么再从中选择$\sum_{i=1}^n a_i + \sum_{i=1}^n b_i$ 最大的方案。
求最终的 D ($\sum_{i=1}^n a_i$) 和 P ($\sum_{i=1}^n b_i$) 以及人选编号（方案）。

这是一道多维的 0/1 背包问题，将 n 个人看作 n 个物品，每种物品只有一个。
考虑到题目要求 D - P 尽量小，D + P 尽量大，我们可以将 D - P 作为“体积”，将 D + P 作为“价值”。
最终用 f[j][k] 表示在前 i 个人里选择了 j 个人，$|D - P|$ 为 k 的 D + P 最大值。

转移方程：f[j][k] = max(f[j][k], f[j][k - (a[i] - b[i])] + a[i] + b[i]);
初值：f[0][0] = 0，其余均为负无穷。
目标：找到一个状态 f[m][k] 满足 k 尽量小，当 k 相同时 f[m][k] 尽量大。

记录路径也很好办，开一个数组，每次递归并记录就好了，要注意的是，由于 D - P 可能为负，也就是 k 可能为负，我们需要将 0 提为 fix，通过计算发现 k 最小为 -20m，那么就令 fix = 20m。这样就保证 k 维始终非负啦。

CODE：
``` c++
#include <cstdio>
#include <cstring>
#include <algorithm>
using namespace std;

int T = 1, n, m, k, a, b, fix;
int f[25][805], D[25][805], v[205], s[205], list[25];

bool select(int j, int k, int i) {
    while (j && D[j][k] != i) {
        k -= v[D[j][k]];
        --j;
    }
    return j != 0;
}

int main() {
    while (scanf("%d%d", &n, &m), n && m) {
        memset(f, -1, sizeof(f));
        memset(D, 0, sizeof(D));
        fix = m * 20, f[0][fix] = 0;  // 为了避免第二维上出现负数，将0提为fix
        for (int i = 1; i <= n; i++) {
            scanf("%d%d", &a, &b);
            v[i] = a - b, s[i] = a + b;
        }
        for (int j = 1; j <= m; j++)
            for (k = 0; k <= 2 * fix; k++)
                if (f[j - 1][k] >= 0) {
                    for (int i = 1; i <= n; i++)
                        if (f[j][k + v[i]] < f[j - 1][k] + s[i]) {
                            if (!select(j - 1, k, i)) {
                                f[j][k + v[i]] = f[j - 1][k] + s[i];
                                D[j][k + v[i]] = i;
                            }
                        }
                }
        for (k = 0; k <= fix; k++)
            if (f[m][fix - k] >= 0 || f[m][fix + k] >= 0) break;
        a = f[m][fix - k] > f[m][fix + k] ? fix - k : fix + k;
        b = a;
        for (int i = 1; i <= m; i++) {
            list[i] = D[m - i + 1][b];
            b -= v[list[i]];
        }
        sort(list + 1, list + m + 1);
        printf("Jury #%d\n", T++);
        printf("Best jury has value %d for prosecution and value %d for defence:\n", (f[m][a] + a - fix) / 2, (f[m][a] - a + fix) / 2);
        for (int i = 1; i <= m; i++) printf(" %d", list[i]);
        printf("\n\n");
    }
    return 0;
}
```

我想本题最妙的就是将 D - P 和 D + P 作为状态，恰好符合了01背包的特点。