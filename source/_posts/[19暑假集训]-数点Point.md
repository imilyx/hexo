---
title: '[19暑假集训]-数点Point'
date: 2019-08-03 23:39:11
tags:
    - 线段树
mathjax: true
hidden: true
---

来源：8.3 T2

[原题](https://codeforces.com/problemset/problem/295/E)

线段树可还行（微笑

发现点的坐标是可以离散化的，所求的式子也是可以数据结构维护的，那么就线段树呗

不想离散化，于是尝试了动态开点～

x结点的答案 = ls结点答案 + rs结点答案 + (ls 与 rs 产生的新贡献)

其中 (ls 与 rs 产生的新贡献) = (ls 点数) ·（rs 区间坐标和）- (rs 点数) ·（ls 区间坐标和）

code :
``` c++
// 不是 CF 原题代码！原题应为注释掉的部分
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 5e6 + 10, o = 1e9;
typedef long long ll;
int n, m, tot = 1, L[N], R[N];
ll x, a[N];
struct node {
	ll ans, num, sum;
}T[N];

node merge(node x, node y) {
	node z;
	z.num = x.num + y.num;
	z.sum = x.sum + y.sum;
	z.ans = x.ans + y.ans + y.sum * x.num - x.sum * y.num;
	return z;
}

void modify(int &x, ll l, ll r, ll pos, int val) {
	if (!x) x = ++tot;
	if (l == r) {
		T[x].num += val, T[x].sum += (pos - o) * val;
		return;
	}
	ll mid = (l + r) >> 1;
	if (pos <= mid) modify(L[x], l, mid, pos, val);
	else modify(R[x], mid + 1, r, pos, val);
	T[x] = merge(T[L[x]], T[R[x]]);
}

node query(int x, ll l, ll r, ll lx, ll rx) {
	if (!x) return T[0];
	if (lx <= l && r <= rx) return T[x];
	ll mid = (l + r) >> 1;
	if (rx <= mid) return query(L[x], l, mid, lx, rx);
	if (lx > mid) return query(R[x], mid + 1, r, lx, rx);
	return merge(query(L[x], l, mid, lx, rx), query(R[x], mid + 1, r, lx, rx));
}

int main() {
	cin >> n;
	int root = 1;
	rep(i, 1, n) {
		scanf("%lld", &x); a[i] = x + o;
		if (a[i] >= 0 && a[i] <= o * 2) {
			modify(root, 0, o * 2, a[i], 1);
		}
	}
	cin >> m;
	while (m--) {
		int ff; ll x, y;
		scanf("%d%lld%lld", &ff, &x, &y);
		if (ff == 1) {
			if (a[x] >= 0 && a[x] <= o * 2) modify(root, 0, o * 2, a[x], -1);
			a[x] = y + o;
			// a[x] += y;
			if (a[x] >= 0 && a[x] <= o * 2) modify(root, 0, o * 2, a[x], 1);
		} else {
			printf("%lld\n", query(root, 0, o * 2, x + o, y + o).ans);
		}
	}
	return 0;
}
```