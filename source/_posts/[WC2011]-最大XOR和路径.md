---
title: '[WC2011]-最大XOR和路径'
date: 2019-08-18 00:20:11
tags:
    - 线性基
mathjax: true
---

[传送门](https://www.luogu.org/problem/P4151)

学了线性基、、挺友好的、、、

首先很容易得出，一个环走两遍没有意义，走过一个子树也没有意义（因为任意一条边都会走过去再走回来），所以路径可以表示为一条链 + 一些环。

我们把每一个环的 XOR 和丢进一个线性基里，答案就是链与线性基的最大异或和。

ps：链不一定要是最长链。假设有链 A 和链 B，两者构成了 1 ~ n ~ 1 的一个环，也就是这个环异或链 A 就能得到链 B。

最近有进步呀！自己都能想想了（。

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

typedef long long ll;
const int N = 1e5 + 10;
ll n, m, vis[N], mark[N];
ll to[N << 1], nxt[N << 1], lnk[N], cnt;
ll val[N << 1], p[65];

void addedge(int x, int y, ll z) {
	to[++cnt] = y, nxt[cnt] = lnk[x], lnk[x] = cnt, val[cnt] = z;
}

void insert(ll x) {
	for (int i = 63; i >= 0; i--) {
		if (!(x & (1ll << i))) continue;
		if (!p[i]) { p[i] = x; break; }
		x ^= p[i];
	}
}

ll query(ll x) {
	ll ret = x;
	for (int i = 63; i >= 0; i--) {
		if ((ret ^ p[i]) > ret) ret ^= p[i];
	}
	return ret;
}

void dfs(int x, ll res) {
	mark[x] = res, vis[x] = 1;
	for (int i = lnk[x]; i; i = nxt[i]) {
		int y = to[i];
		if (!vis[y]) dfs(y, res ^ val[i]);
		else insert(res ^ mark[y] ^ val[i]);
	}
}

int main() {
	// freopen("in.txt", "r", stdin);
	cin >> n >> m;
	rep(i, 1, m) {
		int x, y; ll z; scanf("%d%d%lld", &x, &y, &z);
		addedge(x, y, z), addedge(y, x, z);
	}
	dfs(1, 0);
	printf("%lld\n", query(mark[n]));
	return 0;
}
```