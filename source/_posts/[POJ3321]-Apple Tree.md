---
title: '[POJ3321]-Apple Tree'
date: 2019-02-13 19:20:40
tags: 
    - dfs序
mathjax: true
---

[题目链接](https://vjudge.net/problem/POJ-3321)

这题是要改变一个节点的取值或者求子树节点和。看到子树和，我们就知道dfs序是正解啦。

树状数组维护前缀和～

code :
``` c++
#include <bits/stdc++.h>
#define lowbit(x) ((x) & (-x))
using namespace std;

const int N = 1e5 + 10;
int n, m, x, l[N], r[N], tot, C[N], have[N];
int to[N << 1], nxt[N << 1], lnk[N], cnt;
char s[5];

void add(int u, int v) {
    to[++cnt] = v, nxt[cnt] = lnk[u], lnk[u] = cnt;
}

void dfs(int x) {
    l[x] = ++tot;
    for (int i = lnk[x]; i; i = nxt[i]) dfs(to[i]);
    r[x] = ++tot;
}

void upd(int x, int num) {
    for (; x <= tot; x += lowbit(x)) C[x] += num;
}

int sum(int x) {
    int ret = 0;
    for (; x; x -= lowbit(x)) ret += C[x];
    return ret;
}

int main() {
    scanf("%d", &n);
    for (int i = 1; i < n; i++) {
        int u, v; scanf("%d%d", &u, &v);
        add(u, v);
    }
    dfs(1);
    for (int i = 1; i <= n; i++) {
        have[i] = 1;
        upd(l[i], 1);// upd(r[i], -1);
    }
    scanf("%d", &m);
    while (m--) {
        scanf("%s%d", s, &x);
        if (s[0] == 'C') {
            if (have[x]) upd(l[x], -1);// upd(r[x], 1);
            else upd(l[x], 1);// upd(r[x], -1);
            have[x] ^= 1;
        } else printf("%d\n", sum(r[x]) - sum(l[x] - 1));
    }
    return 0;
}
```