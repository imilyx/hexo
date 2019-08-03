---
title: '[19暑假集训]-染色Color'
date: 2019-08-03 23:30:11
tags:
    - 数据结构
mathjax: true
---

来源：8.3 T1

[原题](https://codeforces.com/problemset/problem/926/J)

数据结构题可还行。。CF 原题，我没做过 QwQ

将线段按左端点排序，使用 set 维护线段，插入时用 lower_bound() 查找这条线段插入的位置，尝试向左向右合并 set 中其它线段。

不会 TLE，是因为合并操作最多 n 次。查询时输出 set 的 size 即可.

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 2e5 + 10;
typedef pair<int, int> pii;
int n;
set<pii> S;

int main() {
	cin >> n;
	rep(i, 1, n) {
		pii p;
		scanf("%d%d", &p.first, &p.second);
		set<pii>::iterator it = S.lower_bound(p);
		if (it != S.begin()) {
			it--;
			if ((*it).second >= p.first) {
				p.first = min(p.first, (*it).first);
				p.second = max((*it).second, p.second);
				S.erase(it++);
			} else it++;
		}
		for (; it != S.end();) {
			if ((*it).first <= p.second) {
				p.second = max((*it).second, p.second);
				S.erase(it++);
			} else break;
		}
		S.insert(p);
		printf("%d ", S.size());
	}
	return 0;
}
```