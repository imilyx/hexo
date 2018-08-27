---
title: '[POJ3322]-Bloxorz I'
date: 2018-08-27 22:50:40
tags: 
    - 广度搜索
mathjax: true
---

[题目链接](https://vjudge.net/problem/POJ-3322)

题意略。

很明显，这是一道广搜题，但是属于较复杂的，写时必须保证思维顺畅，不能出错。

我们用 (x, y, lie) 表示方块的一个状态，其中：

* lie = 0 表示长方形立在 (x, y) 上；

* lie = 1 表示长方形横向躺着，且左半部分位置在 (x, y)；

* lie = 1 表示长方形纵向躺着，且上半部分位置在 (x, y)。

同时我们用 d[x, y, lie] 记录从起始状态到达每个状态的最少步数。

其余要注意各种边界处理，以及小窍门：可以用 const 数组事先记录情况，避免大量 if 带来的错误率。

具体看代码吧，真的复杂。。。

code:
``` c++
#include <cstdio>
#include <queue>
using namespace std;

struct rec { int x, y, lie; };
char s[510][510];
rec st, ed;
int n, m, d[510][510][3];
queue<rec> q;
// 0 ～ 4: 左右上下
const int dx[4] = {0, 0, -1, 1}, dy[4] = {-1, 1, 0, 0};

bool chk(int x, int y) { return x >= 1 && x <= n && y >= 1 && y <= m; }

void parse_st_ed() {
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++)
            if (s[i][j] == 'O') {
                ed.x = i, ed.y = j, ed.lie = 0, s[i][j] = '.';
            } else if (s[i][j] == 'X') {
                for (int k = 0; k < 4; k++) {
                    int x = i + dx[k], y = j + dy[k];
                    if (chk(x, y) && s[x][y] == 'X') {
                        st.x = min(i, x), st.y = min(j, y), st.lie = k < 2 ? 1 : 2;
                        s[i][j] = s[x][y] = '.';
                        break;
                    }
                }
                if (s[i][j] == 'X') {
                    st.x = i, st.y = j, st.lie = 0, s[i][j] = '.';
                }
            }
}

// 这一步非常省力！
// next_x[i, j] 表示在 lie = i 时朝方向 j 滚动后 x 的变化情况
const int next_x[3][4] = { { 0, 0, -2, 1 }, { 0, 0, -1, 1 }, { 0, 0, -1, 2 } };
// next_y[i, j] 表示在 lie = i 时朝方向 j 滚动后 y 的变化情况
const int next_y[3][4] = { { -2, 1, 0, 0 }, { -1, 2, 0, 0 }, { -1, 1, 0, 0 } };
// next_lie[i, j] 表示在 lie = i 时朝方向 j 滚动后 lie 的新值
const int next_lie[3][4] = { { 1, 1, 2, 2 }, { 0, 0, 1, 1 }, { 2, 2, 0, 0 } };

// 滚动是否合法
bool valid(rec next) {
    if (!chk(next.x, next.y)) return 0;
    if (s[next.x][next.y] == '#') return 0;
    if (next.lie == 0 && s[next.x][next.y] == 'E') return 0;
    if (next.lie == 1 && s[next.x][next.y + 1] == '#') return 0;
    if (next.lie == 2 && s[next.x + 1][next.y] == '#') return 0;
    return 1;
}

int bfs() {
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++)
            for (int k = 0; k < 3; k++) d[i][j][k] = -1;
    while (!q.empty()) q.pop();
    d[st.x][st.y][st.lie] = 0;
    q.push(st);
    while (!q.empty()) {
        rec now = q.front(); q.pop();
        for (int i = 0; i < 4; i++) {
            rec next;
            next.x = now.x + next_x[now.lie][i];
            next.y = now.y + next_y[now.lie][i];
            next.lie = next_lie[now.lie][i];
            if (!valid(next)) continue;
            if (d[next.x][next.y][next.lie] == -1) {
                d[next.x][next.y][next.lie] = d[now.x][now.y][now.lie] + 1;
                q.push(next);
                if (next.x == ed.x && next.y == ed.y && next.lie == ed.lie)
                    return d[next.x][next.y][next.lie];
            }
        }
    }
    return -1;  // 千万勿忘！
}

int main() {
    while (~scanf("%d%d", &n, &m) && n) {
        for (int i = 1; i <= n; i++) scanf("%s", s[i] + 1);
        parse_st_ed();
        int ans = bfs();
        if (ans == -1) puts("Impossible");
        else printf("%d\n", ans);
    }
    return 0;
}
```