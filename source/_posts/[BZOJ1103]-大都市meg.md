---
title: '[BZOJ1103]-大都市meg'
date: 2019-02-13 20:20:40
tags: 
    - dfs序
mathjax: true
hidden: true
---

[题目链接](https://www.lydsy.com/JudgeOnline/problem.php?id=1103)

使一条边 $(x, y)$ 权值为 $0$ 等价于给 $y$ 及 $y$ 的子树减一。

求 $x$ 到 $1$ 的路径长度即求区间 $[l[1], l[x]]$ 的值。

code :
``` c++
#include <bits/stdc++.h>
#define lowbit(x) ((x) & (-x))
using namespace std;

const int N = 25e4 + 10;
int n, m, cnt, top, T;
int C[N << 1], lnk[N << 1], to[N << 1], nxt[N << 1];
int st[N], fa[N], l[N], r[N];
char s[5];

void add(int u, int v) {
    to[++cnt] = v, nxt[cnt] = lnk[u], lnk[u] = cnt;
}

void dfs(int u, int fa) {
    l[u] = ++T;
    for (int i = lnk[u]; i; i = nxt[i])
        if (to[i] != fa) dfs(to[i], u);
    r[u] = ++T;
}

void upd(int x, int val) {
    for (; x <= 2 * n; x += lowbit(x)) C[x] += val;
}

int query(int x) {
    int ret = -1;
    for (; x; x -= lowbit(x)) ret += C[x];
    return ret;
}

int main() {
    scanf("%d", &n);
    for (int i = 1; i < n; i++) {
        int u, v; scanf("%d%d", &u, &v);
        add(u, v), add(v, u);
    }
    dfs(1, 0);
    for (int i = 1; i <= n; i++) upd(l[i], 1), upd(r[i], -1);
    scanf("%d", &m);
    for (int i = 1; i <= n + m - 1; i++) {
        scanf("%s", s);
        if (s[0] == 'A') {
            int x, y; scanf("%d%d", &x, &y);
            upd(l[y], -1), upd(r[y], 1);
        } else if (s[0] == 'W') {
            int x; scanf("%d", &x);
            printf("%d\n", query(l[x]));
        }
    }
    return 0;
}
```