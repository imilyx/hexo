---
title: '[PKUWC2018]-Minimax'
date: 2020-08-30 12:15:40
tags: 
    - 概率与期望
    - 线段树合并
    - 整体dp
mathjax: true
---

[传送门](https://www.luogu.com.cn/problem/P5298)

以前好像碰到过类似 dp 套路？离散化，$f[i, j]$ 表示第 $j$ 小的数留到了 $i$ 节点的概率。转移：

$$f[i, j] = f[ls, j] * (p[i] * \sum\limits_{k = 1}^{j - 1} f[rs, k] + (1 - p[i]) * \sum\limits_{k = j + 1}^m f[rs, k]) + f[rs, j] * (p[i] * \sum\limits_{k = 1}^{j - 1} f[ls, k] + (1 - p[i]) * \sum\limits_{k = j + 1}^m f[ls, k])$$

发现每个叶子结点的权值都不同，且都只会贡献一次，**想到线段树合并**（注意要合并的东西必须是 $O(n)$ 及以下级别的，不然上不了线段树合并）。然后发现线段树可以很好地维护区间和、区间乘标记等东西！

（哇线段树合并复杂度是啥呀我又不会了）

好吧来看看，合并两颗线段树的复杂度约等于它们 **重合** 的点数，设树有 $n$ 个节点，线段长度是 $m$，总复杂度就是 $O(nlogm)$（感性理解就是每加进来一个点最多增加 $logm$ 的复杂度）

这类题，即用动态开点线段树优化的二维dp，有个新名词叫做“整体dp”！

code:
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int mod = 998244353, N = 3e5 + 10, M = N * 60, inv = 796898467, P = mod;
typedef long long ll;
int n, m, idx;
int son[N][2], h[N], val[N], ans, ls[M], rs[M], tag[M], f[M], rt[N];

void psd(int x) {
    if (tag[x] != 1) {
        tag[ls[x]] = 1ll * tag[ls[x]] * tag[x] % mod;
        tag[rs[x]] = 1ll * tag[rs[x]] * tag[x] % mod;
        f[ls[x]] = 1ll * f[ls[x]] * tag[x] % mod;
        f[rs[x]] = 1ll * f[rs[x]] * tag[x] % mod;
        tag[x] = 1;
    }
}

void upd(int x) {
    f[x] = (f[ls[x]] + f[rs[x]]) % mod;
}

void insert(int &x, int l, int r, int pos) {
    if (!x) x = ++idx, tag[idx] = 1;
    if (l == r) {
        f[x] = 1; return;  // 概率是 100%
    }
    int mid = (l + r) >> 1;
    if (pos <= mid) insert(ls[x], l, mid, pos);
    else insert(rs[x], mid + 1, r, pos);
    upd(x);
}

int merge(int x, int y, int la, int ra, int lb, int rb, int p) {
    if (!x && !y) return 0;
    if (x) psd(x);
    if (y) psd(y);
    if (x && !y) {
        int t = (1ll * p * lb % mod + 1ll * (1 - p + mod) % mod * rb % mod) % mod;
        f[x] = 1ll * f[x] * t % mod;
        tag[x] = 1ll * tag[x] * t % mod;
        return x;
    }
    if (!x && y) {
        int t = (1ll * p * la % mod + 1ll * (1 - p + mod) % mod * ra % mod) % mod;
        f[y] = 1ll * f[y] * t % mod;
        tag[y] = 1ll * tag[y] * t % mod;
        return y;
    }
    int a = f[ls[x]], b = f[ls[y]];
    int cur = ++idx; tag[cur] = 1;
    ls[cur] = merge(ls[x], ls[y], la, (ra + f[rs[x]]) % mod, lb, (rb + f[rs[y]]) % mod, p);
    rs[cur] = merge(rs[x], rs[y], (la + a) % mod, ra, (lb + b) % mod, rb, p);
    upd(cur);
    return cur;
}

void dfs(int x) {
    if (!son[x][0] && !son[x][1]) {
        insert(rt[x], 1, m, val[x]);
        return;
    }
    if (!son[x][1]) {
        dfs(son[x][0]);
        rt[x] = rt[son[x][0]];
        return;
    }
    dfs(son[x][0]), dfs(son[x][1]);
    int p = 1ll * val[x] * inv % mod;
    rt[x] = merge(rt[son[x][0]], rt[son[x][1]], 0, 0, 0, 0, p);
}

void calc_ans(int x, int l, int r) {
    if (!x) return;
    psd(x);
    if (l == r) {
        ans = (ans + 1ll * l * h[l] % mod * f[x] % mod * f[x] % mod) % mod;
        return;
    }
    int mid = (l + r) >> 1;
    calc_ans(ls[x], l, mid), calc_ans(rs[x], mid + 1, r);
}

int main() {
    cin >> n;
    rep(i, 1, n) {
        int x; scanf("%d", &x);
        if (!son[x][0]) son[x][0] = i;
        else son[x][1] = i;
    }
    rep(i, 1, n) {
        scanf("%d", &val[i]);
        if (!son[i][0] && !son[i][1]) h[++m] = val[i];
    }
    sort(h + 1, h + m + 1);
    rep(i, 1, n)
        if (!son[i][0] && !son[i][1])
            val[i] = lower_bound(h + 1, h + m + 1, val[i]) - h;
    dfs(1);
    calc_ans(rt[1], 1, m);
    printf("%d\n", ans);
    return 0;
}
```