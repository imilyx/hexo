---
title: '[模拟赛题]-克卜勒(kepler)'
date: 2019-02-22 18:30:11
tags:
    - 树状数组
mathjax: true
hidden: true
---

# Description #

Zyh在浩瀚的宇宙中发现了一个神奇的星系。这个星系上很可能有文明的痕迹，因为它的星球的连接方式非常特别。
具体的来说整个星系由若干个小星系组成，这若干个小星系是由若干个星球组成的。星系由n个小星系组成，这n个小星系在这里可以抽象成一个小圆。小星系编号1~n首尾相接形成一个大圆。若将第i个小星系放大，那么看到的就是Ci个星球。这些星球也是首尾相接形成了一个大圆。特别地，每个小星系中有两个星球x,y，分别成为起始星球和结束星球。在整个星系大圆的构成中实际上是由上一个小星系的结束星球连接着下一个小星系的起始星球。

然而星球之间的通信是一个问题。Zyh认为，星球会有两个状态，允许通行和禁止通行。星球的编号即使是在不同的小星系中也是两两不同的，如果是第I个小星系的第J个星球，那么编号应该是[sigma(1<=i<I)Ci]+J。如果星球p要去向星球q，那么如果存在一条路径，这条路径上的星球的状态都是允许通行，那么称这两个星球是可以到达的。然而复杂的是，这些星球都是会改变状态的。Zyh观察了m个改变操作和询问，他想要回答这些询问，于是他找到了你。

# Input #

首先是一个正整数n表示小星系的个数，然后是n行。每行的开始是Ci表示这个小星系的星球个数。接着是Ci+2个数字，分别是S[1] …… S[Ci] x y。S[j]表示第i个小星系的第j个星球的通行状态。0表示禁止通行，1表示允许通行。x和y表示这个小星系的第x第y个分别是起始和结束星球。

然后是一个正整数m，表示操作和询问总数。接下来是m行。如果是操作那么是这样的形式给出：1 x，表示将编号为x的星球状态置反。如果是询问：2 x y，表示询问星球x和y能不能互相到达。如果可以输出Yes，否则输出No。

# Output #

对于每个询问输出对应的答案。

Sample input&output

Input
``` c++
4
3 1 1 0 2 1
2 1 0 2 1
3 0 1 0 1 3
4 0 0 1 1 2 4
12
2 1 2
2 1 4
1 5
2 1 11
1 6
1 7
1 8
2 2 8
1 6
2 2 8
1 10
2 2 8 
```
Output
``` c++
Yes
No
Yes
Yes
No
Yes
```
# Hint #

对于30%的数据 sigma Ci<=1000 , n<=100 , m<=10000

另有10%的数据 Ci<=500000,n=1 , m<=500000

对于100%的数据 sigmaCi<=500000 , n<=10000 , m<=500000 , Ci>1 , x!=y无论是询问还是起始结束星球


# 题解在这里 #

哇，很棒的模拟赛题！

可以用树状数组，或者线段树，维护一个小星系的连通性和大星系的连通性。

具体方法是单点修改和区间查询，为了方便操作，我们规定 0 表示可以通行，1 表示不能通行。如果一段区间和为 0 ，那么表示这段区间都是 0，也就是说是可以通行的。

同时，要注意 “环状” ！两个点之间的路有两种走法的！

实现起来会有些麻烦，函数大法好～

code :
``` c++
#include <bits/stdc++.h>
#define lowbit(x) ((x) & (-(x)))
using namespace std;

const int N = 1e4 + 10, M = 5e5 + 10;
int n, m, c, sum, a[M];
int l[N], r[N], b[M], st[N], ed[N], f[M], g[M];
int flag[N];

void addf(int x, int val) {
    for (; x <= sum; x += lowbit(x)) f[x] += val;
}

void addg(int x, int val) {
    for (; x <= n; x += lowbit(x)) g[x] += val;
}

int getf(int x, int y) {
    int ret = 0;
    for (; y; y -= lowbit(y)) ret += f[y];
    for (x--; x; x -= lowbit(x)) ret -= f[x];
    return ret;
}

int getg(int x, int y) {
    int ret = 0;
    for (; y; y -= lowbit(y)) ret += g[y];
    for (x--; x; x -= lowbit(x)) ret -= g[x];
    return ret;
}

int chk(int x, int y) {
    if (x > y) swap(x, y);
    int z = b[x];
    return !getf(x, y) || !(getf(l[z], x) + getf(y, r[z]));
}

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) {
        scanf("%d", &c);
        l[i] = sum + 1, r[i] = sum + c;
        for (int j = l[i]; j <= r[i]; j++) {
            scanf("%d", &a[j]); a[j] ^= 1;
            b[j] = i;
        }
        scanf("%d%d", &st[i], &ed[i]);
        st[i] += sum, ed[i] += sum;
        sum += c;
    }
    for (int i = 1; i <= sum; i++) if (a[i]) addf(i, 1);
    for (int i = 1; i <= n; i++) {
        int x = st[i], y = ed[i];
        if (x > y) swap(x, y);
        if (getf(x, y) && getf(l[i], x) + getf(y, r[i]))
            addg(i, flag[i] = 1);
    }
    scanf("%d", &m);
    while (m--) {
        int ff;
        scanf("%d", &ff);
        if (ff == 1) {
            int pos; scanf("%d", &pos);
            addf(pos, (a[pos] ^ 1) - a[pos]);
            a[pos] ^= 1;
            int z = b[pos], x = st[z], y = ed[z];
            if (x > y) swap(x, y);
            if (getf(x, y) && getf(l[z], x) + getf(y, r[z])) {
                if (!flag[z]) flag[z] = 1, addg(z, 1);
            } else {
                if (flag[z]) flag[z] = 0, addg(z, -1);
            }
        } else {
            int x, y; scanf("%d%d", &x, &y);
            if (x > y) swap(x, y);
            if (n == 1) {
                if (chk(x, y) || (chk(ed[1], x) && chk(y, st[1])) || (chk(st[1], x) && chk(y, ed[1]))) {
                    puts("Yes"); continue;
                } /* else {
                    puts("No"); continue;
                }*/
            }
            if (b[x] == b[y]) {
                int z = b[x];
                if (chk(x, y) || !(getg(1, z - 1) + getg(z + 1, n)) && (chk(st[z], x) && chk(y, ed[z]) || chk(ed[z], x) && chk(y, st[z]))) {
                    puts("Yes"); continue;
                } else {
                    puts("No"); continue;
                }
            }
            int xx = b[x], yy = b[y];
            if (xx > yy) swap(xx, yy), swap(x, y);
            if (!getg(xx + 1, yy - 1) && chk(x, ed[xx]) && chk(st[yy], y)) {
                puts("Yes"); continue;
            }
            if (!(getg(1, xx - 1) + getg(yy + 1, n)) && chk(st[xx], x) && chk(y, ed[yy])) {
                puts("Yes"); continue;
            }
            puts("No");
        }
    }
    return 0;
}
```