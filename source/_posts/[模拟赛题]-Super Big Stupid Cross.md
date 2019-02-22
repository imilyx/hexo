---
title: '[模拟赛题]-Super Big Stupid Cross'
date: 2019-02-22 19:20:11
tags:
    - 扫描线
mathjax: true
---

# Description #

“我是超级大沙茶”——Mato_No1

为了证明自己是一个超级大沙茶，Mato 神犇决定展示自己对叉(十字型)有多么的了 解。

Mato 神犇有一个平面直角坐标系，上面有一些线段，保证这些线段至少与一条坐标轴 平行。Mato 神犇需要指出，这些线段构成的最大的十字型有多大。

称一个图形为大小为 R(R 为正整数)的十字型，当且仅当，这个图形具有一个中心点， 它存在于某一条线段上，并且由该点向上下左右延伸出的长度为 R 的线段都被已有的线段 覆盖。

你可以假定:没有两条共线的线段具有公共点，没有重合的线段。

【输入格式】

第一行，一个正整数 N，代表线段的数目。

以下 N 行，每行四个整数 x1,y1,x2,y2(x1=x2 或 y1=y2)，描述了一条线段。 

【输出格式】

当不存在十字型时:输出一行“Human intelligence is really terrible”(不包括引号)。

否则:输出一行，一个整数，为最大的 R。 

【样例输入 1】
``` c++
1
0001
```
【样例输出 1】
``` c++
Human intelligence is really terrible
```
【样例输入 2】
``` c++
    3
    -1 0 5 0
    0 -1 0 1
    2 -2 2 2
```
【样例输出 2】
``` c++
2
```
【数据规模与约定】

对于 50%的数据:N≤1000。

对于 100%的数据:1≤N≤100000，所有坐标的范围在-10^9~10^9 中。
后 50%内，所有数据均为随机生成。

# 题解在这里 #

这题真是想不到，想不到啊。。

首先二分 R，每条长度 > 2R 的线段两端减去 R 的长度。然后是用扫描线判断有没有十字形，即从左往右扫，碰到一条横线左端点就将它加入 set，碰到右端点就将它扔出 set；碰到一条竖线就看一看 set 有没有在这条竖线上端点和下端点之间的，如果有，那就是十字形。

code :
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 1e5 + 10;
typedef set<int>::iterator It;
int n, cnt1, cnt2, l, r, ans, nums, numh;
multiset<int> q;
struct node1 { int x, l, r, len; }s[N], ts[N];
struct node2 { int y, l, r, len, id; }h[N], th[N], Th[N];
struct node3 { int p, id; }R[N], L[N];

bool cmp(node1 a, node1 b) { return a.x < b.x; }
bool cmp1(node2 a, node2 b) { return a.l != b.l ? a.l < b.l : a.r < b.r; }
bool cmp2(node2 a, node2 b) { return a.r != b.r ? a.r < b.r : a.l < b.l; }

void prework(int R) {
	nums = numh = 0;
	for (int i = 1; i <= cnt1; i++)
		if (s[i].len >= 2 * R) {
			ts[++nums].x = s[i].x;
			ts[nums].l = s[i].l + R;
			ts[nums].r = s[i].r - R;
			ts[nums].len = s[i].len - 2 * R;
		}
	for (int i = 1; i <= cnt2; i++)
		if (h[i].len >= 2 * R) {
			th[++numh].y = h[i].y;
			th[numh].l = h[i].l + R;
			th[numh].r = h[i].r - R;
			th[numh].len = h[i].len - 2 * R;
			th[numh].id = numh;
		}
	memcpy(Th, th, sizeof(th));
}

bool check(int limit) {
	q.clear();
	q.insert(1e9);
	prework(limit);
	sort(ts + 1, ts + nums + 1, cmp);
	sort(th + 1, th + numh + 1, cmp1);
    for (int i = 1; i <= numh; i++)
        L[i].p = th[i].l, L[i].id = th[i].id;
    sort(th + 1, th + numh + 1, cmp2);
    for (int i = 1; i <= numh; i++)
        R[i].p = th[i].r, R[i].id = th[i].id;
    L[numh + 1].p = R[numh + 1].p = ts[nums + 1].x = 1e9;
    int x = 1, y = 1, z = 1;
    while (x <= numh || y <= numh || z <= nums) {
        int a = 0, b = 0, c = 0;
        if (L[x + a].p <= R[y].p && L[x + a].p <= ts[z].x) {
            q.insert(Th[L[x].id].y);
            a++;
            while (L[x + a - 1].p == L[x + a].p) q.insert(Th[L[x + a].id].y), a++;
        }
        if (ts[z + c].x <= L[x].p && ts[z + c].x <= R[y].p) {
            It s = q.lower_bound(ts[z].l);
            if (*s <= ts[z].r) return 1;
            c++;
            while (ts[z + c - 1].x == ts[z + c].x) {
                It s = q.lower_bound(ts[z + c].l);
                if (*s <= ts[z + c].r) return 1;
                c++;
            }
        }
        if (R[y].p <= L[x].p && R[y].p <= ts[z].x) {
            q.erase(Th[R[y].id].y);
            b++;
            while (R[b + y - 1].p == R[b + y].p) q.erase(Th[R[b + y].id].y), b++;
        }
        x += a, y += b, z += c;
    }
    return 0;
}

int main() {
	scanf("%d", &n);
	for (int i = 1; i <= n; i++) {
        int x, y, x2, y2;
		scanf("%d%d%d%d", &x, &y, &x2, &y2);
		if (x > x2) swap(x, x2);
		if (y > y2) swap(y, y2);
		if (x == x2) {
			s[++cnt1].x = x, s[cnt1].l = y, s[cnt1].r = y2, s[cnt1].len = y2 - y;
		} else {
			h[++cnt2].y = y, h[cnt2].l = x, h[cnt2].r = x2, h[cnt2].len = x2 - x;
		}
	}
	l = 1, r = 1e9, ans = 1e9;
	while (l <= r) {
		int mid = (l + r) >> 1;
		if (check(mid)) ans = mid, l = mid + 1;
		else r = mid - 1;
	}
	if (ans != 1e9) printf("%d\n", ans);
	else puts("Human intelligence is really terrible");
	return 0;
}
```