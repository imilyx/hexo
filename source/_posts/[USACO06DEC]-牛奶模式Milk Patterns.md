---
title: '[USACO06DEC]-牛奶模式Milk Patterns'
date: 2019-08-17 23:50:11
tags:
    - 后缀数组
mathjax: true
hidden: true
---

[传送门](https://www.luogu.org/problem/P2852)

前置知识：height数组。

首先需要知道，按一个字符串每个后缀前 k 个字符排序等同于给字符串进行后缀排序。

其实就是判断是否存在至少 K 个排名连续的后缀满足两两的 LCP 长度为 x，这个 x 可以二分。

当然也可以单调队列，答案就是任意 k - 1 个连续的 height 的最小值的最大值。

code : (单调队列)
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
#define per(i, x, y) for (int i = x; i >= y; i--)
using namespace std;

const int N = 2e4 + 10;
int n, m, K, t[N], a[N];
int cntA[N], cntB[N], A[N], B[N], tsa[N];
int sa[N], rk[N], height[N];

void lisan() {
	sort(t + 1, t + n + 1);
	m = unique(t + 1, t + n + 1) - t - 1;
	rep(i, 1, n)
		a[i] = lower_bound(t + 1, t + n + 1, a[i]) - t;
}

void build_sa() {
	rep(i, 0, m) cntA[i] = cntB[i] = 0;
	rep(i, 1, n) cntA[a[i]]++;
	rep(i, 1, n) cntA[i] += cntA[i - 1];
	per(i, n, 1) sa[cntA[a[i]]--] = i;
	
	rk[sa[1]] = 1;
	rep(i, 2, n) {
		rk[sa[i]] = rk[sa[i - 1]];
		if (a[sa[i]] != a[sa[i - 1]]) rk[sa[i]]++;
	}
	
	for (int l = 1; rk[sa[n]] < n; l <<= 1) {
		rep(i, 0, n) cntA[i] = cntB[i] = 0;
		rep(i, 1, n) {
			cntA[A[i] = rk[i]]++;
			cntB[B[i] = (i + l <= n ? rk[i + l] : 0)]++;
		}
		rep(i, 1, n) cntB[i] += cntB[i - 1];
		per(i, n, 1) tsa[cntB[B[i]]--] = i;
		rep(i, 1, n) cntA[i] += cntA[i - 1];
		per(i, n, 1) sa[cntA[A[tsa[i]]]--] = tsa[i];
		
		rk[sa[1]] = 1;
		rep(i, 2, n) {
			rk[sa[i]] = rk[sa[i - 1]];
			if (A[sa[i]] != A[sa[i - 1]] || B[sa[i]] != B[sa[i - 1]]) rk[sa[i]]++;
		}
	}
	for (int i = 1, j = 0; i <= n; i++) {
		if (j) --j;
		while (a[i + j] == a[sa[rk[i] - 1] + j]) ++j;
		height[rk[i]] = j;
	}
}

int main() {
	// freopen("in.txt", "r", stdin);
	cin >> n >> K;
	rep(i, 1, n) scanf("%d", &t[i]), a[i] = t[i];
	lisan();
	build_sa();
	
	int q[N], l = 1, r = 0;
	int ans = 0;
	rep(i, 1, n) {
		while (l <= r && q[l] <= i - (K - 1)) ++l;
		while (l <= r && height[q[r]] >= height[i]) --r;
		q[++r] = i;
		if (i >= K) ans = max(ans, height[q[l]]);
	}
	printf("%d\n", ans);
	return 0;
}
```