---
title: '[POJ1742]-Coins'
date: 2018-05-18 20:13:40
tags:
    - 背包
---

https://vjudge.net/problem/POJ-1742

题意：给定 n 个数，值分别为 ai，个数分别为 ci，选择若干面值相加，若能组成 S，便称面值 S 能被拼成。求 1～m 中有多少个数能被拼成。

从个数限制 ci 可以看出，这是多重背包模型。
用 Boolean 数组 f[i] 表示 i 能否被拼成。1～n 枚举使用每种值，同时 1～m 枚举当前若用了 a[i]，j 能否拼成。由于需要满足多重背包个数的限制————多重背包也正体现于此，我们需要用数组 used[j] 来表示当前拼成 j 需要的 a[i] 的个数。
可以发现，只有两类情况：
1. 即 f[j] 已经为 true，表示前 i - 1 个数就能拼成 j，used[j] = 0；
2. 即 f[j] 为 false，则需要考虑 f[j - a[i]]，若 f[j - a[i]] = true，同时 used[j - a[i]] < c[i]，那么 f[j] = true。

CODE：
``` c++
#include <cstdio>
#include <cstring>
#include <algorithm>
using namespace std;

const int N = 105, M = 100005;
int n, m, a[N], c[N], used[M], ans;
bool f[M];

int main() {
    while (scanf("%d%d", &n, &m), n && m) {
        for (int i = 1; i <= n; i++) scanf("%d", &a[i]);
        for (int i = 1; i <= n; i++) scanf("%d", &c[i]);
        memset(f, false, sizeof(f));
        f[0] = true, ans = 0;
        for (int i = 1; i <= n; i++) {
            for (int j = 0; j <= m; j++) used[j] = 0;
            for (int j = a[i]; j <= m; j++)
                if (!f[j] && f[j - a[i]] && used[j - a[i]] < c[i])
                    f[j] = true, used[j] = used[j - a[i]] + 1;
        }
        for (int i = 1; i <= m; i++)
            if (f[i]) ans++;
        printf("%d\n", ans);
    }
    return 0;
}
```

这道题只关注是否可行，而非一般背包的最优解问题，算一种特殊的性质，used[] 辅助实现限制，注意不要忘了 f[] 与 used[] 的清零。同时利用贪心思想，仅当 f[j] = false，不得不使用第 i 种数值时才进行转移，保证不漏掉可行解。