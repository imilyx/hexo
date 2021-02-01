---
title: '[模拟赛题]-Babystep'
date: 2019-02-22 19:30:11
tags:
    - 对偶图
    - 并查集
mathjax: true
hidden: true
---

# Description #

从前有一个 Baby。

从前还有一个网格图。

Baby 喜欢爆炸。

Baby 偶尔会炸掉网格图中的一条边(u, v)。之后他会尝试从 u 走到 v。 如果他成功地从 u 走到 v，他会很高兴;否则他会找人打架。 从第二次爆炸开始，根据 Baby 此时心情的不同，Baby 会炸掉不同的边。 你被要求编写一个程序，对于每次爆炸，给出此时 Baby 是否还能从 u 到 v。

【输入格式】

第一行，一个整数 R，代表网格图是 R*R 的。

第二行，一个整数 N，代表操作数。

第三行，四个整数 x1,y1,x2,y2，描述了第一次爆炸的边。

以下 N-1 行，每行四个整数 x1,y1,x2,y2,x1’,y1’,x2’,y2’，分别描述 Baby 高兴时炸的边和
想找人打架时炸的边。

【输出格式】

输出 N 行，如果本次爆炸后 Baby 高兴，输出一行“HAHA”，否则输出一行“DAJIA”。 

【样例输入 1】
``` c++
2
2
1112 11211222
```
【样例输出 1】
``` c++
HAHA
DAJIA
```
【数据规模与约定】

对于 40%的数据，R≤50。

对于 100%的数据，1≤R≤500，1≤N≤2R^2-2R。

对于 100%的数据，1≤x1,x2,x1’,x2’≤R，1≤y1,y2,y1’,y2’≤R。 对于所有对边的描述，要么 x1=x2 且|y1-y2|=1，要么 y1=y2 且|x1-x2|=1。 保证所有边至多被删除一次。

均匀地，对于 50%的数据，x1=x1’，y1=y1’，x2=x2’，y2=y2’。 请注意输入输出规模较大。

# 题解在这里 #

妙题好评！Human intelligence is really terrible 😢出题人脑洞可真大

注意到是网格图，一个格子四周有四个点。破开一条边等同于将相邻两个格子连通。如果两个点不能互相到达，则必然会有两个不同的连通块。

什么时候会有这种情况呢？当有环状的方格（一圈的那种）存在时，环状中间那坨点就是被隔离开来的。就是这种情况。

我们把题目转换一下，俗称“转对偶图”。当某个破边的操作会使得两个已经在一个连通块的格子再次连通时，就会形成环状，这时输出 “DAJIA”。

code :
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 1505;
int n, m, flag, tot;
int r[N][N], fa[N * N], x[5], y[5];

int idx(int x, int y) {
    if (!x || !y || x == n || y == n) return 0;
    return (x - 1) * (n - 1) + y;
}

int getfa(int x) { return fa[x] == x ? x : fa[x] = getfa(fa[x]); }

int merge(int x, int y) {
    x = getfa(x), y = getfa(y);
    if (x == y) return 0;
    fa[x] = y;
    return 1;
}

int main() {
    // freopen("babystep.in", "r", stdin);
    // freopen("babystep.out", "w", stdout);

    scanf("%d%d", &n, &m);
    for (int i = 1; i < n; i++) for (int j = 1; j < n; j++) {
        r[i][j] = ++tot;
        fa[tot] = tot;
    }
    flag = 1;
    for (int i = 1; i <= m; i++) {
        scanf("%d%d%d%d", &x[1], &y[1], &x[2], &y[2]);
        if (i > 1) scanf("%d%d%d%d", &x[3], &y[3], &x[4], &y[4]);
        if (!flag) {
            swap(x[1], x[3]), swap(y[1], y[3]), swap(x[2], y[2]), swap(x[4], y[4]);
        }
        if (x[1] == x[2]) {
            if (y[1] > y[2]) swap(y[1], y[2]);
            x[1]--; y[2] = y[1];
        } else {
            if (x[1] > x[2]) swap(x[1], x[2]);
            y[1]--; x[2] = x[1];
        }
        int id1 = idx(x[1], y[1]), id2 = idx(x[2], y[2]);
        int X = getfa(id1), Y = getfa(id2);
        if (X != Y) {
            fa[X] = Y;
            puts("HAHA"); flag = 1;
        } else {
            puts("DAJIA"); flag = 0;
        }
    }
    return 0;
}
```