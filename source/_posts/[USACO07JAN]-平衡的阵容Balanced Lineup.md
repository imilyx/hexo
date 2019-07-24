---
title: '[USACO07JAN]-平衡的阵容Balanced Lineup'
date: 2019-07-23 23:25:11
tags:
    - RMQ
mathjax: true
---

[传送门](https://www.luogu.org/problem/P2880)

50000 的数据范围，nlogn 足矣= =

RMQ 来写一波～ 这种数据结构真是好写又实用！

code :
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 5e4 + 10, M = 2e5 + 10;
int n, q, a[N], Mn[N][20], Mx[N][20];

int main() {
	// freopen("in.txt", "r", stdin);
	scanf("%d%d", &n, &q);
	for (int i = 1; i <= n; i++) {
		scanf("%d", &a[i]);
		Mn[i][0] = Mx[i][0] = a[i];
	}
	for (int j = 1; (1 << j) <= n; j++)
		for (int i = 1; i + (1 << j) - 1 <= n; i++) {
			Mn[i][j] = min(Mn[i][j - 1], Mn[i + (1 << (j - 1))][j - 1]);
			Mx[i][j] = max(Mx[i][j - 1], Mx[i + (1 << (j - 1))][j - 1]);
		}
	while (q--) {
		int l, r; scanf("%d%d", &l, &r);
		int dis = r - l + 1;
		int t = 0;
		while (1 << (t + 1) <= dis) ++t;
		int a = max(Mx[l][t], Mx[r - (1 << t) + 1][t]);
		int b = min(Mn[l][t], Mn[r - (1 << t) + 1][t]);
		printf("%d\n", a - b);
	}
	return 0;
}
```