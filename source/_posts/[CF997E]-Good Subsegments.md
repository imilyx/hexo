---
title: '[CF997E]-Good Subsegments'
date: 2019-11-05 18:55:40
tags: 
    - 线段树
mathjax: true
---

[传送门](https://codeforces.com/contest/997/problem/E)

这题太神了！！真是好题！！！

显然：区间 [l, r] 是好的，当且仅当 (Max - Min) - (r - l) = 0.

我们可以将询问区间按右端点从小到大排序，依次作为“当前的右端点”。

于是我们维护每个位置到“当前右端点”的 (Max - Min) - (r - l)，维护它们的最小值和最小值个数。

重要的 trick：一个区间 [l, r] 所有子区间的答案可以随着“当前右端点” r0 的增加而实时统计，累加 query(l, r0).

仅仅这样做，线段树操作后存储的就只是当前的答案，所以我们还需要用标记，每次 pushdown 的时候为线段树累加上前一次的答案。

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
#define ls (x << 1)
#define rs (x << 1 | 1)
using namespace std;

typedef long long ll;
const int N = 120010;
int n, Q, a[N], s1[N], s2[N], top1, top2;
struct node {
    int l, r, id;
    bool operator < (const node &x) const { return r < x.r; }
}q[N];
int tag[N << 2], tg[N << 2], mn[N << 2], t[N << 2];
ll sum[N << 2], ans[N];

void build(int x, int l, int r) {
    mn[x] = l;  // at first: (mx - mn) - (r - l) = 0 - (0 - l) = l
    t[x] = 1;
    if (l == r) return;
    int mid = (l + r) >> 1;
    build(ls, l, mid), build(rs, mid + 1, r);
}

void pushup(int x) {
    mn[x] = min(mn[ls], mn[rs]);
    t[x] = 0;

    t[x] = (mn[x] == mn[ls] ? t[x] + t[ls] : t[x]);
    t[x] = (mn[x] == mn[rs] ? t[x] + t[rs] : t[x]);

    sum[x] = sum[ls] + sum[rs];
}

void psd(int x) {
    if (tag[x]) {
        mn[ls] += tag[x], mn[rs] += tag[x];
        tag[ls] += tag[x], tag[rs] += tag[x];
        tag[x] = 0;
    }
    if (tg[x]) {
        if (mn[x] == mn[ls])
            sum[ls] += 1ll * t[ls] * tg[x], tg[ls] += tg[x];
        if (mn[x] == mn[rs])
            sum[rs] += 1ll * t[rs] * tg[x], tg[rs] += tg[x];
        tg[x] = 0;
    }
}

void modify(int x, int l, int r, int lx, int rx, int val) {
    if (lx <= l && r <= rx) {
        mn[x] += val; tag[x] += val; return;
    }
    int mid = (l + r) >> 1;
    psd(x);
    if (lx <= mid) modify(ls, l, mid, lx, rx, val);
    if (rx > mid) modify(rs, mid + 1, r, lx, rx, val);
    pushup(x);
}

ll query(int x, int l, int r, int lx, int rx) {
    if (lx <= l && r <= rx) return sum[x];
    psd(x);
    ll mid = (l + r) >> 1, ret = 0;
    if (lx <= mid) ret = query(ls, l, mid, lx, rx);
    if (rx > mid) ret += query(rs, mid + 1, r, lx, rx);
    return ret;
}

int main() {
    cin >> n;
    rep(i, 1, n) scanf("%d", &a[i]);
    cin >> Q;
    rep(i, 1, Q) scanf("%d%d", &q[i].l, &q[i].r), q[i].id = i;
    sort(q + 1, q + Q + 1);
    build(1, 1, n);

    int pos = 1;
    rep(tr, 1, n) {
        mn[1]--, tag[1]--;  // 随着 r 的增加，最小值减小

        while (top1 && a[s1[top1]] > a[tr])
            modify(1, 1, n, s1[top1 - 1] + 1, s1[top1], a[s1[top1]] - a[tr]), --top1;
        s1[++top1] = tr;

        while (top2 && a[s2[top2]] < a[tr])
            modify(1, 1, n, s2[top2 - 1] + 1, s2[top2], a[tr] - a[s2[top2]]), --top2;
        s2[++top2] = tr;

        sum[1] += t[1], tg[1]++;

        while (pos <= Q && q[pos].r == tr) {
            ans[q[pos].id] = query(1, 1, n, q[pos].l, tr);
            ++pos;
        }
    }
    rep(i, 1, Q) printf("%lld\n", ans[i]);
    return 0;
}
```