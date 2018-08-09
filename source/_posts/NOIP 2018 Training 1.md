---
title: 'NOIP 2018 Training 1'
date: 2018-06-23 14:50:40
tags: 
    - 比赛
mathjax: true
---

### A. Count Good Prefixes

https://vjudge.net/problem/CodeChef-GOODPREF

给定整数 n 和字符串 s，字符串仅包含‘a’和‘b’两种字符。考虑字符串 t = s + s + · · · + s，即将 s 重复 n 遍得到的字符串。
请求出 t 有多少非空前缀满足其中‘a’的出现次数严格多余‘b’的出现次数。$1 \leq n \leq 10^9$ 。

可以证明代码中的做法是不会超时的：可以发现字符串长度很小，$10^3$ ，如果一遍下来 a 字符数已经大于 b 字符数了，那么长度遍过后 b 字符数无论在字符的哪一个位置都不能赶上 a 了，跳出循环；若 b 字符数大于 a 字符数了，一样操作；若相等，那么只要将字符所有位置中 a 字符数大于 b 字符数的情况数乘上剩下的遍数即可，跳出循环。

CODE：
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 1005;
char s[N];
int n, freq;

void solve() {
    long long ret = 0;
    int a = 0, b = 0;
    while (freq--) {
        for (int i = 1; i <= n; i++) {
            if (s[i] == 'a') a++;
            else b++;
            if (a > b) ret++;
        }
        if (a == b) {
            ret *= freq + 1;
            break;
        } else if (a - b > n) {
            ret += 1ll * n * freq;  // freq 在 while 循环中是递减的！
            break;
        } else if (b - a > n) break;
    }
    printf("%lld\n", ret);
    return;
}

int main() {
    int T;
    scanf("%d", &T);
    while (T--) {
        scanf("%s%d", s + 1, &freq);
        n = strlen(s + 1);
        solve();
    }
    return 0;
}
```

### B. Andryusha and Colored Balloons

https://vjudge.net/problem/CodeForces-781A

大概就是用尽量少的颜色给一棵树染色，相邻三个节点颜色不能相同。记录一下父节点和祖父节点的颜色就好了。

CODE：
``` c++
#include <bits/stdc++.h>
using namespace std;

const int mo = 1e9 + 7, N = 200005;
typedef long long ll;

vector<int> nxt[N];
int color[N], n, ans;

void dfs(int x, int fa) {
    int cnt = 1;
    for (int i = 0; i < nxt[x].size(); i++) {
        if (nxt[x][i] != fa) {
            while (cnt == color[x] || cnt == color[fa]) cnt++;
            color[nxt[x][i]] = cnt++;
        }
    }
    for (int i = 0; i < nxt[x].size(); i++)
        if (nxt[x][i] != fa) dfs(nxt[x][i], x);
}

int main() {
    scanf("%d", &n);
    for (int i = 1; i < n; i++) {
        int u, v;
        scanf("%d%d", &u, &v);
        nxt[u].push_back(v);
        nxt[v].push_back(u);
    }
    color[1] = 1;
    dfs(1, 0);
    for (int i = 1; i <= n; i++) ans = max(ans, color[i]);
    printf("%d\n", ans);
    for (int i = 1; i <= n; i++) printf("%d ", color[i]);
    putchar('\n');
    return 0;
}
```

### C. Median Sum

https://vjudge.net/problem/AtCoder-3857

有 n 个数，那么考虑每一个数取或不取，共有 $2^n - 1$ 种和，求其中的中值。

用 bitset 来表示每一个数能否被表示成 n 个数中任意个数的和的形式，能，1；不能，0。
例如 n = 2 ，两个数分别为 3 和 5，那么这个 bitset 中第 3 位和第 5 位为 1，第 8 位也为 1，而第 8 位赋值方法是 3 << 5。

CODE：
``` c++
#include <bits/stdc++.h>
using namespace std;

int n, x, tot, ans;
bitset<4000005> bit;

int main() {
    scanf("%d", &n);
    bit.set(0);
    for (int i = 1; i <= n; i++) {
        scanf("%d", &x);
        bit = bit | (bit << x);
        tot += x;
    }
    for (ans = (tot + 1) / 2; !bit[ans]; ans++);
    printf("%d\n", ans);
    return 0;
}
```

### D. Salvage Robots

https://vjudge.net/problem/AtCoder-2045

有一张 $h * w$ 的地图，上面有 $h * w$ 个格子，格子里为 o 的表示有机器人，为 E 的表示是出口。每次你可以将所有机器人向上下左右任意一个方向移动一步（注意是所有机器人），碰到出口的机器人获救，碰到墙壁的或在墙壁外面的机器人死亡。求最多可以救多少个机器人，无步数限制。

因为运动是相互的，所以移动机器人就相当于移动墙壁和出口。

file:///Users/imily/Desktop/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-06-23%20%E4%B8%8B%E5%8D%882.29.45.png

由此图得：

file:///Users/imily/Desktop/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-06-23%20%E4%B8%8B%E5%8D%882.35.03.png

红色部分表示已经死亡的区域。最终我们用 $f_{a, b, c, d}$ 表示坐标(a, b) 到坐标(c, d) 得矩阵已经走完获得的机器人，每次转移时向上下左右某个方向扩展一行即可。

CODE：
```  c++
#include <bits/stdc++.h>
using namespace std;

const int N = 105;
char s[N][N];
int r[N][N], c[N][N];
int f[N * N >> 2][N * N >> 2];
int h, w, X, Y;

int encode(int l, int r, int M) {return r * M + l;}  // 编码

void chkmax(int& x, int v) {
    x = max(x, v);
}

int dfs(int lX, int rX, int lY, int rY) {
    int cX = encode(lX, rX, X), cY = encode(lY, rY, Y);
    if (f[cX][cY] >= 0) return f[cX][cY];
    f[cX][cY] = 0;
    if (lX < X - 1)
        chkmax(f[cX][cY], dfs(lX + 1, rX, lY, rY) + (rX + 1 <= X - lX - 1 ? max(0, r[X - lX - 1][min(Y + rY, w - lY)] - r[X - lX - 1][max(Y - lY, rY + 1) - 1]) : 0));
    if (rX < h - X)
        chkmax(f[cX][cY], dfs(lX, rX + 1, lY, rY) + (X + rX + 1 <= h - lX ? max(0, r[X + rX + 1][min(Y + rY, w - lY)] - r[X + rX + 1][max(Y - lY, rY + 1) - 1]) : 0));
    if (lY < Y - 1)
        chkmax(f[cX][cY], dfs(lX, rX, lY + 1, rY) + (rY + 1 <= Y - lY - 1 ? max(0, c[min(X + rX, h - lX)][Y - lY - 1] - c[max(X - lX, rX + 1) - 1][Y - lY - 1]) : 0));
    if (rY < w - Y)
        chkmax(f[cX][cY], dfs(lX, rX, lY, rY + 1) + (Y + rY + 1 <= w - lY ? max(0, c[min(X + rX, h - lX)][Y + rY + 1] - c[max(X - lX, rX + 1) - 1][Y + rY + 1]) : 0));
    return f[cX][cY];
}

int main() {
    scanf("%d%d", &h, &w);
    for (int i = 1; i <= h; i++) scanf("%s", s[i] + 1);
    for (int i = 1; i <= h; i++)
        for (int j = 1; j <= w; j++)
            if (s[i][j] == 'E') X = i, Y = j;
    for (int i = 1; i <= h; i++)
        for (int j = 1; j <= w; j++) {
            r[i][j] = r[i][j - 1] + (s[i][j] == 'o');
            c[i][j] = c[i - 1][j] + (s[i][j] == 'o');
        }
    memset(f, -1, sizeof(f));
    printf("%d\n", dfs(0, 0, 0, 0));
    return 0;
}
```