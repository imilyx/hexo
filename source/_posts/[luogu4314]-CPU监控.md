---
title: '[luogu4314]-CPU监控'
date: 2019-02-22 18:15:40
tags: 
    - 线段树
mathjax: true
---

[题目链接](https://www.luogu.org/problemnew/show/P4314)

AC之旅真是坎坷啊。。一上来，咦，这不是模版题嘛，深蓝题是群众误评嘛 qwq

然后敲完一交，哎哟啧，爆零哇。。。去翻了翻题解，发现这个问题还真是隐蔽。

题解里是这样说的：

“按原来的方法会有一点 bug  ，就是很有可能会出现这样的情况：对于某个结点 i ，在它的标记还没有下放的时候，它的父亲又下放了新的标记给它，于是就将原来的标记覆盖了，丢失了原来的那一次修改的值，这样在查询历史最大值的时候就有可能出现错误的答案。

“如果只记录区间历史最大值显然不能下放，如果单纯更新区间加，区间赋值最大值，可能会出现历史最大值更新不及时的情况。如先赋值很大值，未来得及下放，又赋值很小，导致子区间历史最大值不能更新。又如如果区间加只取最大值，可能会只取最大值，导致实际上忽视了一些使区间加变小的操作。”

如果次次下放标记，那显然复杂度太高了。

为了不错过每一次操作产生的贡献，我们对于每一个区间 x 存储它从上一次 pushdown 到现在的最大加法操作和最大赋值操作，这样显然是正确的。

总之还是很棒的啊，至少比之前的所以模版题好！！！就是要做这样的（虐）题 ( ´▽｀)

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
const int N = 1e5 + 10, inf = 1e9;
int n, m;
char s[5];

struct node {
    ll add, c;
    ll mx, hx;
    ll ha, hc;  // h = history
}t[N << 2];

void pushup(int x) {
    t[x].mx = max(t[x << 1].mx, t[x << 1 | 1].mx);
    t[x].hx = max(t[x << 1].hx, t[x << 1 | 1].hx);
}

void psd(int x) {
    for (int i = 0; i <= 1; i++) {
        int s = x << 1 | i;  // s = son
        t[s].hx = max(t[s].hx, max(t[s].mx + t[x].ha, t[x].hc));
        if (t[s].c != -inf)
            t[s].hc = max(t[s].hc, t[s].c + t[x].ha);
        else t[s].ha = max(t[s].ha, t[s].add + t[x].ha);
        if (t[x].add) {
            if (t[s].c != -inf) t[s].c += t[x].add;
            else t[s].add += t[x].add;
            t[s].mx += t[x].add;
        }
        if (t[x].c != -inf) {
            t[s].mx = t[s].c = t[x].c;
            t[s].add = 0;
        }
        t[s].hc = max(t[s].hc, max(t[s].c, t[x].hc));
        t[s].ha = max(t[s].ha, t[s].add);
    }
    t[x].hc = t[x].c = -inf;
    t[x].add = t[x].ha = 0;
}

void build(int x, int l, int r) {
    t[x].add = t[x].ha = 0;
    t[x].c = t[x].hc = -inf;
    if (l == r) {
        ll tmp; scanf("%lld", &tmp);
        t[x].mx = t[x].hx = tmp;
        return;
    }
    int mid = (l + r) >> 1;
    build(x << 1, l, mid);
    build(x << 1 | 1, mid + 1, r);
    pushup(x);
}

void modify(int x, int l, int r, int lx, int rx, ll val) {
    if (l < r) psd(x);
    if (lx <= l && r <= rx) {
        t[x].mx += val;
        t[x].add += val;
        t[x].ha += val;
        t[x].hx = max(t[x].hx, t[x].mx);
        return;
    }
    int mid = (l + r) >> 1;
    if (lx <= mid) modify(x << 1, l, mid, lx, rx, val);
    if (rx > mid) modify(x << 1 | 1, mid + 1, r, lx, rx, val);
    pushup(x);
}

void change(int x, int l, int r, int lx, int rx, ll val) {
    if (l != r) psd(x);
    if (lx <= l && r <= rx) {
        t[x].c = val;
        t[x].mx = val;
        t[x].hc = val;
        t[x].hx = max(t[x].hx, val);
        return;
    }
    int mid = (l + r) >> 1;
    if (lx <= mid) change(x << 1, l, mid, lx, rx, val);
    if (rx > mid) change(x << 1 | 1, mid + 1, r, lx, rx, val);
    pushup(x);
}

ll queryhis(int x, int l, int r, int lx, int rx) {
    if (l < r) psd(x);
    if (lx <= l && r <= rx) return t[x].hx;
    int mid = (l + r) >> 1;
    ll sum = -1e9;
    if (lx <= mid) sum = queryhis(x << 1, l, mid, lx, rx);
    if (rx > mid) sum = max(sum, queryhis(x << 1 | 1, mid + 1, r, lx, rx));
    return sum;
}

ll querynow(int x, int l, int r, int lx, int rx) {
    if (l < r) psd(x);
    if (lx <= l && r <= rx) return t[x].mx;
    int mid = (l + r) >> 1;
    ll sum = -1e9;
    if (lx <= mid) sum = querynow(x << 1, l, mid, lx, rx);
    if (rx > mid) sum = max(sum, querynow(x << 1 | 1, mid + 1, r, lx, rx));
    return sum;
}

int main() {
    scanf("%d", &n);
    build(1, 1, n);
    scanf("%d", &m);
    while (m--) {
        scanf("%s", s);
        if (s[0] == 'A') {
            int l, r; scanf("%d%d", &l, &r);
            printf("%d\n", queryhis(1, 1, n, l, r));
        } else if (s[0] == 'Q') {
            int l, r; scanf("%d%d", &l, &r);
            printf("%d\n", querynow(1, 1, n, l, r));
        } else if (s[0] == 'P') {
            int l, r, z; scanf("%d%d%d", &l, &r, &z);
            modify(1, 1, n, l, r, z);
        } else {
            int l, r, z; scanf("%d%d%d", &l, &r, &z);
            change(1, 1, n, l, r, z);
        }
    }
    return 0;
}
```