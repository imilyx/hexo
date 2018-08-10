---
title: '[CF559C]-Gerald and Giant Chess'
date: 2018-08-10 17:37:40
tags: 
    - 计数DP
    - 排列组合
mathjax: true
---

https://vjudge.net/problem/CodeForces-559C

题意：给定一个 $H * W$ 的棋盘，棋盘上只有 N 个格子是黑色的，其余都是白色的。在棋盘左上角有一个卒，每一步可以向下或向右移动一格，并且不能移动到黑色格子中，求这个卒从左上角移到右下角，一共有多少种可能的路线。
$1 \leq H, W \leq 10^5$, $1 \leq N \leq 2000$. 答案对 1e9 + 7 取模。

我们发现，H 与 W 很大，而 N 只有 2000，可以考虑如何依靠黑色格子进行计数。

如果允许走进任何颜色的格子，那么从左上角走到右下角的方案数就是 $C_{H + W - 2}^{H - 1}$。这个就是组合计数里的多重集组合数。我们可以理解为，从 1 到 H 的几何距离是 $|H - 1|$，从 1 到 W 的几何距离是 $|W - 1|$，那么从 (1, 1) 到 (H, W) 的距离（步数）就是 $H - 1 + W - 1 = H + W - 2$，其中有 $H - 1$ 步都是纵向的。

若我们再求出从左上角到右下角经过了至少一个黑格子的路线数量，两者相减即为答案。我们可以求出从左上角到第 i 个黑格子的方案数，且途中不经过其他黑格子，表示为 F[i]。把所有黑格子按照行、列坐标递增的顺序排序。

$$F[i] = C_{x_i + y_i - 2}^{x_i - 1} - \sum\limits_{j = 1}^{i - 1}F[j] * C_{x_i - x_j + y_i - y_j}^{x_i - x_j}, x_i \geq x_j\ and\ y_i \geq y_j$$

上式的含义是，枚举 j 作为左上角到 $(x_i, y_i)$ 经过的**第一个**黑格子，路线为 F[j], 从第 j 个黑格子到第 i 个黑格子的方案数为组合数。这样可以保证 i 黑格是该路线遇到的第一个黑格。

**在求解计数类动态规划问题时，通常要找到一个“基准点”，围绕这个基准点构造一个不可划分的“整体”，以避免子问题的重叠**

code:
``` c++
#include <bits/stdc++.h>
#define x first
#define y second
using namespace std;

typedef long long ll;
const int N = 2010, mod = 1e9 + 7;
pair<int, int> a[N];
int h, w, n, f[N];
ll Fac[200010], inv[200010];

int C(int n, int m) {
    return Fac[n] * inv[m] % mod * inv[n - m] % mod;
}

ll quick_pow(ll a, ll b) {
    ll ret = 1;
    for (; b; b >>= 1) {
        if (b & 1) ret = ret * a % mod;
        a = a * a % mod;
    } return ret;
}

int main() {
    Fac[0] = 1, inv[0] = 1;
    for (int i = 1; i <= 200000; i++) {
        Fac[i] = Fac[i - 1] * i % mod;
        inv[i] = quick_pow(Fac[i], mod - 2);
    }
    scanf("%d%d%d", &h, &w, &n);
    for (int i = 1; i <= n; i++) scanf("%d%d", &a[i].x, &a[i].y);
    sort(a + 1, a + n + 1);
    a[n + 1].x = h, a[n + 1].y = w;
    for (int i = 1; i <= n + 1; i++) {
        f[i] = C(a[i].x + a[i].y - 2, a[i].x - 1);
        for (int j = 1; j < i; j++) {
            if (a[j].x > a[i].x || a[j].y > a[i].y) continue;
            f[i] = (f[i] - (ll)f[j] * C(a[i].x - a[j].x + a[i].y - a[j].y, a[i].x - a[j].x)) % mod;
        }
    }
    printf("%d\n", (f[n + 1] + mod) % mod);
    return 0;
}
```

真是太妙了！组合数学总是给我很舒服的感觉，恰到好处的去除了重叠部分。计数类DP就是要求“不重不漏”，而这一点恰是需要严密的数学思维的。