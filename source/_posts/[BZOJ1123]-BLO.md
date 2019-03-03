---
title: '[BZOJ1123]-BLO'
date: 2019-03-03 22:30:40
tags: 
    - 图的连通
mathjax: true
---

[题目链接](https://www.lydsy.com/JudgeOnline/problem.php?id=1123)

根据割点的定义，若节点 i 不是割点，则把与 i 相连的边删除后，只有 i 与其他 n - 1 个点不连通，而其他 n - 1 个点之间还都是连通的，（注意是有序点对！）则此时答案为 2(n - 1).

否则，去掉 i 后，会分成若干个连通块，我们应求出每个连通块的大小，两两相乘再相加。

我们可以在 Tarjan 算法执行深度优先遍历的过程中，顺便求出搜索树每棵子树的大小，这样，如果 y in Son(x), low[y] >= dfn[x], 关于 x 的边全部断了后 y 及 y 的子树就是一个连通分量，它的大小应该很好求吧？

code :
``` c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
const int N = 1e5 + 10, M = 5e5 + 10;
int lnk[N], nxt[M << 1], to[M << 1];
int dfn[N], low[N], size[N], cut[N];
ll ans[N];
int n, m, tot, num;


void add(int x, int y) {
    to[++tot] = y, nxt[tot] = lnk[x], lnk[x] = tot;
}

void tarjan(int x) {
    dfn[x] = low[x] = ++num; size[x] = 1;
    int flag = 0, sum = 0;
    for (int i = lnk[x]; i; i = nxt[i]) {
        int y = to[i];
        if (!dfn[y]) {
            tarjan(y);
            size[x] += size[y];
            low[x] = min(low[x], low[y]);
            if (low[y] >= dfn[x]) {
                flag++;
                ans[x] += (ll)size[y] * (n - size[y]);
                sum += size[y];
                if (x != 1 || flag > 1) cut[x] = 1;
            }
        } else low[x] = min(low[x], dfn[y]);
    }
    if (cut[x])
        ans[x] += (ll)(n - sum - 1) * (sum + 1) + n - 1;
    else ans[x] = 2 * (n - 1);
}

int main() {
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= m; i++) {
        int x, y; scanf("%d%d", &x, &y);
        add(x, y), add(y, x);
    }
    tarjan(1);
    for (int i = 1; i <= n; i++) printf("%lld\n", ans[i]);
    return 0;
}
```