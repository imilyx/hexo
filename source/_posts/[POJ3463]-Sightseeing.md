---
title: '[POJ3463]-Sightseeing'
date: 2018-10-03 20:56:40
tags: 
    - 最短路
mathjax: true
---

题意：求从 s 到 t 最短路和比最短路长度大1的路径的总数。

这题是个最短路变形。

首先可以把题意理解为求最短路数量和次短路数量，如果次短路长度是最短路长度+1，那么答案就是两者之和，否则就是最短路数量。

Dijkstra，开两个数组分别记录最短路数量和次短路数量。

code:
``` c++
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <queue>
using namespace std;

const int N = 1010, M = 10010;
typedef pair<int, int> pii;
int T, n, m, lnk[N], cnt, dis[N], dis1[N], num[N], num1[N];
bool inq[N << 1];
struct edge {
    int to, nxt, val;
}e[M];

void Dijkstra(int st) {
    priority_queue<pii, vector<pii>, greater<pii> >q;
    memset(inq, 0, sizeof(inq));
    memset(dis, 0x3f, sizeof(dis));
    dis[st] = 0;
    num[st] = 1;
    q.push(make_pair(dis[st], st));
    while (!q.empty()) {
        int x = q.top().second, w = q.top().first, cnt;
        q.pop();
        if (inq[x]) continue;
        inq[x] = 1;
        if (x > n) x -= n, cnt = num1[x];
        else cnt = num[x];
        for (int i = lnk[x]; i; i = e[i].nxt) {
            int y = e[i].to;
            if (dis[y] > w + e[i].val) {
                dis1[y] = dis[y];
                num1[y] = num[y];
                dis[y] = w + e[i].val;
                num[y] = cnt;
                q.push(make_pair(dis[y], y));
                q.push(make_pair(dis1[y], y + n));
            } else if (dis[y] == w + e[i].val) {
                num[y] += cnt;
            } else if (dis1[y] > w + e[i].val) {
                dis1[y] = w + e[i].val;
                num1[y] = cnt;
                q.push(make_pair(dis1[y], y + n));
            } else if (dis1[y] == w + e[i].val) {
                num1[y] += cnt;
            }
        }
    }
}

int main() {
    scanf("%d", &T);
    while (T--) {
        scanf("%d%d", &n, &m);
        memset(lnk, 0, sizeof(lnk));
        cnt = 0;
        while (m--) {
            int x, y, z;
            scanf("%d%d%d", &x, &y, &z);
            e[++cnt].to = y, e[cnt].nxt = lnk[x], lnk[x] = cnt, e[cnt].val = z;
        }
        int s, t;
        scanf("%d%d", &s, &t);
        Dijkstra(s);
        if (dis1[t] - 1 == dis[t]) num[t] += num1[t];
        printf("%d\n", num[t]);
    }
    return 0;
}
```