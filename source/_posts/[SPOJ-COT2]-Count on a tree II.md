---
title: '[SPOJ-COT2]-Count on a tree II'
date: 2019-08-09 19:50:40
tags: 
    - 树上莫队
mathjax: true
hidden: true
---

[传送门](https://www.luogu.org/problem/SP10707)

处理询问的话，当然是莫队了，在树上就是树上莫队啦（莫队上树？

这题树上莫队是在欧拉序上操作的，不知道有没有在 dfs 序上的。可以用树链剖分在求欧拉序的同时求 LCA。若 x、y 两点的 LCA 不为 x 或 y，x 到 y 的欧拉序间是不会有他们的 LCA 的，需要特判加上。x 到 y 的欧拉序间出现两次的点视为不出现，可以用异或解决。

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 1e5 + 10;
int n, m, a[N], unit, t[N], now, tot;
int sz[N], son[N], dep[N], f[N], tp[N];
int st[N], ed[N], dfn, road[N];
vector<int> g[N];
bool used[N];
struct ques {
	int x, y, ans, id, lca;
	bool operator < (const ques &a) const {
		return x / unit != a.x / unit ? x / unit < a.x / unit : y < a.y;
	}
}q[N];

void lisan() {
	sort(t + 1, t + n + 1);
	int num = unique(t + 1, t + n + 1) - t - 1;
	rep(i, 1, n) a[i] = lower_bound(t + 1, t + num + 1, a[i]) - t;
}

void dfs1(int x, int fa) {
	st[x] = ++dfn, road[dfn] = x;
	f[x] = fa;
	sz[x] = 1;
	int Mx = -1;
	for (int i = 0; i < g[x].size(); i++) {
		int y = g[x][i];
		if (y == fa) continue;
		dep[y] = dep[x] + 1;
		dfs1(y, x);
		sz[x] += sz[y];
		if (Mx < sz[y]) Mx = sz[y], son[x] = y;
	}
	ed[x] = ++dfn, road[dfn] = x;
}

void dfs2(int x, int topfa) {
	tp[x] = topfa;
	if (son[x]) dfs2(son[x], topfa);
	for (int i = 0; i < g[x].size(); i++) {
		int y = g[x][i];
		if (y != f[x] && y != son[x]) dfs2(y, y);
	}
}

int get_LCA(int x, int y) {
	while (tp[x] != tp[y]) {
		if (dep[tp[x]] < dep[tp[y]]) swap(x, y);
		x = f[tp[x]];
	}
	return dep[x] < dep[y] ? x : y;
}

int exist[N];
void add(int x) {
	if (++exist[x] == 1) tot++;
}

void del(int x) {
	if (--exist[x] == 0) tot--;
}

void upd(int x) {
	used[x] ? del(a[x]) : add(a[x]);
	used[x] ^= 1;
}

bool cmp_id(ques a, ques b) {
	return a.id < b.id;
}

int main() {
	// freopen("in.txt", "r", stdin);
	cin >> n >> m;
	unit = (int)sqrt(n);
	rep(i, 1, n) scanf("%d", &a[i]), t[i] = a[i];
	lisan();
	rep(i, 1, n - 1) {
		int x, y; scanf("%d%d", &x, &y);
		g[x].push_back(y), g[y].push_back(x);
	}
	dep[1] = 1; dfs1(1, 0), dfs2(1, 1);
	
	rep(i, 1, m) {
		int x, y; scanf("%d%d", &x, &y);
		if (st[x] > st[y]) swap(x, y);
		int xy = get_LCA(x, y);
		if (xy == x) q[i].x = st[x], q[i].y = st[y];
		else q[i].x = ed[x], q[i].y = st[y], q[i].lca = xy;
		q[i].id = i;
	}
	sort(q + 1, q + m + 1);
	
	int L = 1, R = 0;
	rep(i, 1, m) {
		while (L < q[i].x) upd(road[L++]);
		while (L > q[i].x) upd(road[--L]);
		while (R < q[i].y) upd(road[++R]);
		while (R > q[i].y) upd(road[R--]);
		if (q[i].lca) // lca != x
			upd(q[i].lca);
		q[i].ans = tot;
		if (q[i].lca) upd(q[i].lca);
	}
	sort(q + 1, q + m + 1, cmp_id);
	rep(i, 1, m) {
		printf("%d\n", q[i].ans);
	}
	return 0;
}
```