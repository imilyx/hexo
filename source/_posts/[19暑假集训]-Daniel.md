---
title: '[19暑假集训]-Daniel'
date: 2019-08-03 23:30:11
tags:
    - 最短路
mathjax: true
---

来源：8.2 T2

[原题](http://www.51nod.com/Challenge/Problem.html#problemId=1693)

其实之前有做过类似的“数形结合”，也就是用最短路来解决。

对于本题，复制和粘贴是不好处理的东西，但用最短路就好表示许多。对于一个数 x：

* 连 x -> x - 1 ，边权为 1，表示删除操作；
* 连 x -> i · x ，边权为 i，将复制和粘贴两个操作一起处理。跑 Spfa 据说比 Dij 快。

有一个十分有用的优化：只连 i 为质数的边。可以证明这样距离并不会更远。

然后有人说打表找规律，发现对于本题的 N ，i 都是 <= 13 的，这样边数就大大减少了。我想这样奇奇怪怪的性质以后模拟赛应该经常有吧。。

code :
``` c++
// 并不是 51nod 原题的代码，而是暑假集训的代码，不过差异不大--就是多了个快速幂
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

typedef long long ll;
const int mod = 998244353, N = 2e6 + 10;
const int prime[10] = {2, 3, 5, 7, 11, 13};
ll n, ans, dis[N];
bool inq[N]; 

ll quick_pow(ll a, ll b) {
	ll ret = 1;
	for (; b; b >>= 1) {
		if (b & 1) ret = ret * a % mod;
		a = a * a % mod;
	} return ret;
}

void spfa() {
	queue<int> q;
	q.push(1);
	memset(dis, 0x3f, sizeof(dis));
	dis[1] = 0, inq[1] = 0;
	while (q.size()) {
		int x = q.front(); q.pop(); inq[x] = 0;
		for (int i = 0; i < 6 && prime[i] * x < n + 5; i++) {
			int y = prime[i] * x;
			if (dis[y] > dis[x] + prime[i]) {
				dis[y] = dis[x] + prime[i];
				if (!inq[y]) inq[y] = 1, q.push(y);
			}
		}
		int y = x - 1;
		if (dis[y] > dis[x] + 1) {
			dis[y] = dis[x] + 1;
			if (!inq[y]) inq[y] = 1, q.push(y);
		}
	}
}

int main() {
	cin >> n;
	spfa();
	ll ans = 0;
	rep(i, 1, n) {
		ans = (ans + dis[i] * quick_pow(19260817, n - i) % mod) % mod;
	}
	printf("%lld\n", ans);
	return 0;
}
```