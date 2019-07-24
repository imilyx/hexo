---
title: '[LG1265]-公路修建'
date: 2019-04-16 23:10:40
tags: 
    - 最小生成树
mathjax: true
---

[题目链接](https://www.luogu.org/problemnew/show/P1265)

第一条规则肯定是没用滴！（因为没有说不能申请已修建的道路）

第二条也是没用滴！（滑稽

因为假设 AB < BC，B 选 AB；BC < AC, C 选 BC；如果要成环，必须 AC < AB，不成立。

所以这实际上就是个最小生成树。。。因为边较多，是稠密图，我们用 Prim（算法不难学哦！）

code :
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 5010;
typedef long long ll;
int n;
bool vis[N];
struct node { ll x, y; }p[N];
ll d[N];

ll sqr(ll x) { return x * x; }
ll dis(int a, int b) {
	return (p[a].x - p[b].x) * (p[a].x - p[b].x) + (p[a].y - p[b].y) * (p[a].y - p[b].y);
}

void prim() {
	memset(d, 0x3f, sizeof(d));
	memset(vis, 0, sizeof(vis));
	d[1] = 0;
	for (int i = 1; i < n; i++) {  // i: the num of edge
		int x = 0;
		for (int j = 1; j <= n; j++)
			if (!vis[j] && (!x || d[j] < d[x])) x = j;
		vis[x] = 1;
		for (int y = 1; y <= n; y++)
			if (!vis[y]) d[y] = min(d[y], dis(x, y));
	}
}

int main() {
	scanf("%d", &n);
	for (int i = 1; i <= n; i++) scanf("%lld%lld", &p[i].x, &p[i].y);
	prim();
	double ans = 0;
	for (int i = 1; i <= n; i++) ans += sqrt((double)d[i]);
	printf("%.2lf\n", ans);
	return 0;
}
```