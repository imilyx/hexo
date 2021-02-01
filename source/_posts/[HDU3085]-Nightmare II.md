---
title: '[HDU3085]-Nightmare II'
date: 2018-08-29 20:15:40
tags: 
    - 广度搜索
mathjax: true
hidden: true
---

[题目链接](https://vjudge.net/problem/HDU-3085)

题意：有 1 个男孩，1 个女孩，2 个鬼。男孩每秒可以跑三步，女孩每秒可以跑一步，每个鬼占据的区域每秒可以向四周扩张两个单位距离，并且无视墙的阻挡，即所有在第 k 秒后与鬼的曼哈顿距离不超过 2k 的位置都会被鬼占领。求男孩与女孩能否会合，若能，输出最短会合时间。

双向 BFS 思想，即男孩与女孩同时扩展。建立两个队列。在每一轮中，男孩 BFS 三层，女孩 BFS 一层。

code:
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

int T, r, c, step;
char s[810][810];
struct node { int x, y; }m, g, z[2];
const int dx[4] = { 1, -1, 0, 0 }, dy[4] = { 0, 0, 1, -1 };
queue<node> q[2], qt;

bool chk(node t) {
    if (t.x < 1 || t.x > r || t.y < 1 || t.y > c) return false;
    for (int i = 0; i < 2; i++)
        if (abs(t.x - z[i].x) + abs(t.y - z[i].y) <= 2 * step || s[t.x][t.y] == 'X' || s[t.x][t.y] == '\0')
            return false;
    return true;
}

bool bfs(int mark, int num, char st, char ed) {
    node a, b;
    qt = q[mark];
    while (num--) {
        while (!qt.empty()) {
            a = qt.front(); qt.pop(); q[mark].pop();
            if (!chk(a)) continue;
            for (int i = 0; i < 4; i++) {
                b = a;
                b.x += dx[i], b.y += dy[i];
                if (!chk(b) || s[b.x][b.y] == st) continue;
                if (s[b.x][b.y] == ed) return true;
                s[b.x][b.y] = st;
                q[mark].push(b);
            }
        }
        qt = q[mark];
    }
    return false;
}

int solve() {
    step = 0;
    while (!q[0].empty()) q[0].pop();
    while (!q[1].empty()) q[1].pop();
    while (!qt.empty()) qt.pop();
    q[0].push(m), q[1].push(g);
    while (!q[0].empty() || !q[1].empty()) {
        step++;
        bool flag1 = bfs(0, 3, 'M', 'G');
        bool flag2 = bfs(1, 1, 'G', 'M');
        if (flag1 || flag2) return step;
    }
    return -1;
}

int main() {
    cin >> T;
    while (T--) {
        cin >> r >> c;
        int k = 0;
        rep(i, 1, r) {
            scanf("%s", s[i] + 1);
            rep(j, 1, c) {
                if (s[i][j] == 'M') m.x = i, m.y = j;
                if (s[i][j] == 'G') g.x = i, g.y = j;
                if (s[i][j] == 'Z') z[k].x = i, z[k++].y = j;
            }
        }
        cout << solve() << endl;
    }
    return 0;
}
```