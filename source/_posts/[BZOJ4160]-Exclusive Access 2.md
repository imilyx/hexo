---
title: '[BZOJ4160]-Exclusive Access 2'
date: 2019-02-05 17:17:40
tags: 
mathjax: true
---

题意：给出 $N$ 个点 $M$ 条边的无向图，定向得到有向无环图，使得最长路最短。$N \leq 15$, $M \leq 100$.

这题真好啊！

是比赛 $T2$。一开始觉得没想法，然后码了个暴力，结果一个子任务中有一个点T了，一分都没有……

现在要正经地讲正解了！首先，有向无环图是一个切入点。我们对于第 i 个点，设它的深度为 $di$. 因为“无环”，所以对于每一条边 $< v1, v2 >$, $d1$ 永远小于 $d2$。

假设，这样的图存在环，那么 $d1 < d2 < d3 < ... < dk < d1$, 这不成立，所以该图不存在环。

在这样的图中，最长链为 $dmax - dmin$. Hint！要输出 $ans - 1$ 哦。

为了使 $dmax - dmin$ 最小，我们要使图的深度最小。怎么办呢？二进制DP。

我们设 $f(node)$ 为选择了一些点时的最小深度，其中 $node$ 为 $N$ 位二进制数表示每个点是否取。设 $leaves$ 为该图的叶子结点，那么，$f(node) = min{f(node - leaves) + 1}$.

叶子结点彼此之间是不连边的，所以可以预处理出 $ok(node)$ 表示当 $node$ 这些点能否为 $leaves$。

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

int m, mp[30], cnt, G[30][30], f[1 << 15], ok[1 << 15];
char s1[10], s2[10];

int main() {
    scanf("%d", &m);
    memset(mp, -1, sizeof(mp));
    for (int i = 1; i <= m; i++) {
        scanf("%s%s", s1, s2);
        if (mp[s1[0] - 'A'] == -1) mp[s1[0] - 'A'] = cnt++;
        if (mp[s2[0] - 'A'] == -1) mp[s2[0] - 'A'] = cnt++;
        G[mp[s1[0] - 'A']][mp[s2[0] - 'A']] = G[mp[s2[0] - 'A']][mp[s1[0] - 'A']] = 1;
    }
    memset(f, 0x3f, sizeof(f));
    f[0] = 0;
    for (int i = 0; i < (1 << cnt); i++) {
        ok[i] = 1;
        for (int j = 0; j < cnt; j++)
            for (int k = 0; k < cnt; k++)
                if (j != k && (i & (1 << j)) && (i & (1 << k)) && G[j][k]) {
                    ok[i] = 0; break;
                }
    }
    for (int i = 0; i < (1 << cnt); i++)
        for (int t = i; t; t = (t - 1) & i)
            if (ok[t])
                f[i] = min(f[i], f[i - t] + 1);
    printf("%d\n", f[(1 << cnt) - 1] - 2);
    return 0;
}
```