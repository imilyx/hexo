---
title: 'Codeforces Round 516 Div2'
date: 2018-10-15 22:30:40
tags: 
    - 比赛
mathjax: true
---

[比赛链接](http://codeforces.com/contest/1064)

### A. Make a triangle!
-----

学过三角形就行了～

### B. Equations of Mathematical Magic
-----

a - (a ^ x) - x = 0 ===> a ^ x = a - x, 即 x 二进制位中的 1 是 a 中的子集。这也可以通过打表发现。所以就是记 cnt 为 a 中二进制位为 1 的个数，答案就是 $2^{cnt}$.

### C. Oh Those Palindromes
-----

可以发现，将相同的数字排列在一起，回文字串数目最大。

### D. Labyrinth
-----

裸的BFS应该过不去，可以用双端队列优化。因为我们希望左右的步数尽量不要减少，我们把左右的走步push_back，上下的走步push_front。对deque又有了深一层的了解！

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 2010;
int n, m, r, c, nx, ny;
char s[N][N];
bool vis[N][N];
struct node {
    int x, y, nx, ny;
};
deque<node> q;

int bfs() {
    int cnt = 0;
    while (!q.empty()) {
        node now = q.front(); q.pop_front();
        int x = now.x, y = now.y;
        int nx = now.nx, ny = now.ny;
        if (vis[x][y]) continue;
        vis[x][y] = 1;
        cnt++;
        if (x - 1 > 0 && s[x - 1][y] != '*') q.push_front((node){x - 1, y, nx, ny});
        if (x + 1 <= n && s[x + 1][y] != '*') q.push_front((node){x + 1, y, nx, ny});
        if (y - 1 > 0 && s[x][y - 1] != '*' && nx) q.push_back((node){x, y - 1, nx - 1, ny});
        if (y + 1 <= m && s[x][y + 1] != '*' && ny) q.push_back((node){x, y + 1, nx, ny - 1});
    }
    return cnt;
}

int main() {
    cin >> n >> m >> r >> c >> nx >> ny;
    for (int i = 1; i <= n; i++) cin >> (s[i] + 1);
    q.push_front((node){r, c, nx, ny});
    cout << bfs() << endl;
    return 0;
}
```