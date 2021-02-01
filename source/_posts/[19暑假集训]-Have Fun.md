---
title: '[19暑假集训]-Have Fun'
date: 2019-08-04 22:50:11
tags:
    - 博弈
mathjax: true
hidden: true
---

$\sout{NOT\ FUN\ AT\ ALL}$

[原题](https://www.luogu.org/problem/P4643)

说是博弈，其实是思维题。若一条边两端都是黑色，则小 L 分数 += (两端点权 + 该边边权)；两端都是白色则小 G
分数 += (两端点权 + 该边边权)；否则小 L 分数 += (L 的点权 + (1 / 2)该边边权)，小 G 分数 += (G 的点权 + (1 / 2)该边边权)。

这是不影响正确性的！因为最后算的是分数差，若一条边一端黑一端白，那么算差时就是（白点点权 - 黑点点权 + (1 / 2)该边边权 - (1 / 2)该边边权), 没问题滴～

那么对于每个点，按照其点权与周围边的 (1 / 2)边权和 排序，小 G 选奇数位，小 L 选偶数位。

注意！(1 / 2) 这种下取整会影响精度，所以建议一开始所有边权点权都变为两倍，最后输出答案时除以二。

code :
``` c++
// luogu_4643
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 1e4 + 10;
int n, m, v[N], ans;

bool cmp(int a, int b) { return a > b; }

int main() {
	cin >> n >> m;
	rep(i, 1, n) {
		int w; cin >> w; v[i] = (w << 1);
	}
	rep(i, 1, m) {
		int a, b, c; scanf("%d%d%d", &a, &b, &c);
		v[a] += c, v[b] += c;
	}
	sort(v + 1, v + n + 1, cmp);
	rep(i, 1, n)
		ans += v[i] - v[i + 1], i++;
	printf("%d\n", ans / 2);
	return 0;
}
```