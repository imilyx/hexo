---
title: '[LG4551]-最长异或路径'
date: 2019-10-12 19:20:40
tags: 
    - Trie树
mathjax: true
---

[传送门](https://www.luogu.org/problem/P4551)

【 新技能get√￣ 】

首先有个很重要的常识：$x$ 到 $y$ 的异或值为 $x$ 到根的异或值异或上 $y$ 到根的异或值。其正确性显然。

那么题目就变为：选两个点，使得它们的异或值异或起来最大。

（我居然还想到线性基了。。智商不行呀，不是什么异或都是线性基的，线性基是多个异或，不是两个异或。。

重点来了：$01$ 字典树是处理异或问题的利器，对于一个值 $x$，为了使它与另一个值 $y$ 异或后最大，根据贪心的思想，显然尽量使它们的二进制最高位不同，然后次高位不同...blabla

那么把所有节点到根结点的异或值建成一棵 $01$ 字典树就行啦。复杂度 $O(NlogV)$

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 1e5 + 10;
int n, dis[N], ch[31 * N][2], idx, mark[31 * N], ans;
vector<int> nxt[N], val[N];

void add(int x, int y, int z) {
    nxt[x].push_back(y), val[x].push_back(z);
}

void dfs(int x, int fa) {
    for (int i = 0; i < nxt[x].size(); i++) {
        int y = nxt[x][i];
        if (y == fa) continue;
        dis[y] = dis[x] ^ val[x][i];
        dfs(y, x);
    }
}

void insert(int x) {
    int u = 0;
    for (int i = 30; i >= 0; i--) {
        int c = ((x >> i) & 1);
        if (!ch[u][c]) ch[u][c] = ++idx;
        u = ch[u][c];
    }
    mark[u] = 1;
}

int query(int x) {
    int u = 0, ret = 0;
    for (int i = 30; i >= 0; i--) {
        int c = (((x >> i) & 1) ^ 1);
        if (ch[u][c]) u = ch[u][c], ret += (1 << i);
        else u = ch[u][c ^ 1];
    }
    return ret;
}

int main() {
    cin >> n;
    rep(i, 1, n - 1) {
        int x, y, z; scanf("%d%d%d", &x, &y, &z);
        add(x, y, z), add(y, x, z);
    }
    dfs(1, 0);
    rep(i, 1, n) insert(dis[i]);
    rep(i, 1, n) ans = max(ans, query(dis[i]));
    printf("%d\n", ans);
    return 0;
}
```