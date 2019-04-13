---
title: '[USACO09FEB]-改造路Revamping Trails'
date: 2019-04-13 19:30:11
tags:
    - 最短路
mathjax: true
---

[题目链接](https://www.luogu.org/problemnew/show/P2939)

又 get 了新姿势——分层图！

[这个讲的好](https://blog.csdn.net/qq_40736036/article/details/85041838)

然后写的时候要注意空间大小!

分层图的普遍规律就是，最短路，然后可以选择边权进行操作（如赋值、减半边权等等）。

code :
``` c++
#include <bits/stdc++.h>
using namespace std;

typedef pair<int, int> pii;
const int N = 1e4 + 10, M = 4200010;
priority_queue<pii, vector<pii>, greater<pii> >pq;
int n, m, K;
int to[M], nxt[M], lnk[M], cnt, val[M], dis[M], vis[M];

void add(int x, int y, int z) {
	to[++cnt] = y, nxt[cnt] = lnk[x], lnk[x] = cnt, val[cnt] = z;
}

void dijkstra() {
	memset(dis, 0x3f, sizeof(dis));
	dis[1] = 0;
    memset(vis, 0, sizeof(vis));
	pq.push(make_pair(0, 1));
	while (!pq.empty()) {
		int x = pq.top().second; pq.pop();
		if (vis[x]) continue;
		vis[x] = 1;
		for (int i = lnk[x]; i; i = nxt[i]) {
			int y = to[i];
			if (dis[y] > dis[x] + val[i]) {
				dis[y] = dis[x] + val[i];
				pq.push(make_pair(dis[y], y));
			}
		}
	}
}

int main() {
	scanf("%d%d%d", &n, &m, &K);
	for (int i = 1; i <= m; i++) {
		int x, y, z; scanf("%d%d%d", &x, &y, &z);
		add(x, y, z), add(y, x, z);
		for (int j = 1; j <= K; j++) {
			add(j * n + x, j * n + y, z);
			add(j * n + y, j * n + x, z);
			add((j - 1) * n + x, j * n + y, 0);
			add((j - 1) * n + y, j * n + x, 0);
		}
	}
	dijkstra();
	int ans = dis[n];
	for (int i = 1; i <= K; i++)
		ans = min(ans, dis[i * n + n]);
	printf("%d\n", ans);
	return 0;
}
```