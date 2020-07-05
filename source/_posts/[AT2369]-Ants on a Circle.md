---
title: "[AT2369]-Ants on a Circle"
date: 2020-07-04 19:15:40
tags: 
    - 思维
mathjax: true 
---

两人碰面后方向相反速度不变——这种题有个名字：弹性碰撞（还挺形象

网络上也有类似的：https://www.cnblogs.com/liuchanglc/p/12700770.html#_label6

弱化版1：（上面博客T1）要求所有最终的位置，从小到大输出，不要求一一对应

解法：可以看作相遇的蚂蚁们穿过了彼此，发生碰撞了

弱化版2：（上面博客 T2）

解法：碰撞的过程可以看作交换编号，因此可以得出结论：编号为 i 的蚂蚁碰到 x 个逆向的蚂蚁后编号变为 i + x。

本题：一个道理，可以看作一只蚂蚁穿过 0 ～ l - 1，所有编号都会左移/右移一位

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 1e5 + 10;
typedef long long ll;
ll n, L, T, a[N];

int main() {
	cin >> n >> L >> T;
	rep(i, 1, n) {
		int w; scanf("%lld%d", &a[i], &w);
		a[i] += (w == 1 ? T : -T);
	}
	int pos = 0;
	rep(i, 1, n) {
		pos += a[i] / L - (a[i] % L < 0);
		a[i] = (a[i] % L + L) % L;
	}
	sort(a + 1, a + n + 1);
	pos = (pos % n + n) % n + 1;
	rep(i, 1, n) {
		printf("%lld\n", a[pos]);
		pos = (pos == n ? 1 : pos + 1);
	}
	return 0;
}
```