---
title: 'USACO20OPEN (Gold) 题解'
date: 2020-09-02 17:45:40
tags: 
    - 计数
    - 思维
mathjax: true
---

## T1. Haircut
-----

设每个点初始贡献是 v[i], 那么发现 v[i] 一定是到了某个时刻（其实就是 h[i]）刷一下变成 0 了，树状数组维护。

``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
#define lowbit(x) ((x) & (-(x)))
#define int long long
using namespace std;

const int N = 1e5 + 10;
typedef long long ll;
int n, a[N], ans[N], C[N], num[N];

void add(int x, int v) {
    for (; x <= n + 1; x += lowbit(x)) C[x] += v;
}

int query(int x) {
    if (!x) return 0;
    int ret = 0;
    for (; x; x -= lowbit(x)) ret += C[x];
    return ret;
}

signed main() {
    cin >> n;
    rep(i, 1, n) {
        scanf("%lld", &a[i]);
        ++a[i];
    }
    int tot = 0;
    rep(i, 1, n) {
        num[i] = i - 1 - query(a[i]);
        tot += num[i];
        add(a[i], 1);
    }
    memset(C, 0, sizeof(C));
    rep(i, 1, n)
        add(1, -num[i]), add(a[i] + 1, num[i]);
    int lst = 0;
    rep(i, 1, n) {
        printf("%lld\n", query(i) + tot);
    }
    return 0;
}
```

## T2. Favorite Colors
-----

比较暴力的思路，并查集 + 启发式合并，就是很暴力的将每个点的子节点都并起来，O(n log n)

``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 2e5 + 10;
int n, m, idx, fa[N], col[N];
vector<int> g[N], t[N];

int getfa(int x) {
    return fa[x] == x ? x : fa[x] = getfa(fa[x]);
}

void dfs(int x) {
    if (g[x].size() < 2) return;
    t[x] = g[x];
    g[x].clear();
    int rt = t[x][0];
    rt = getfa(rt);
    for (int i = 1; i < t[x].size(); i++) {
        int y = t[x][i];
        y = getfa(y);
        if (rt == y) continue;
        if (g[rt].size() >= g[y].size()) {
            for (int j = 0; j < g[y].size(); j++) g[rt].push_back(g[y][j]);
            g[y].clear();
            fa[y] = rt;
        } else {
            for (int j = 0; j < g[rt].size(); j++) g[y].push_back(g[rt][j]);
            g[rt].clear();
            rt = fa[rt] = y;
        }
    }
    g[x].push_back(rt);
    dfs(rt);
}

int main() {
    cin >> n >> m;
    rep(i, 1, n) fa[i] = i;
    rep(i, 1, m) {
        int x, y; scanf("%d%d", &x, &y);
        g[x].push_back(y);
    }
    rep(i, 1, n) dfs(i);
    rep(i, 1, n) {
        int x = getfa(i);
        if (!col[x]) col[x] = ++idx;
        printf("%d\n", col[x]);
    }
    return 0;
}
```

## T3. Exercise
-----

k = lcm(环长)，而 n = sum(环长)

考虑 lcm 这个东西本质是把质因数每一位取最高次幂乘起来

[这个老清楚了](https://www.cnblogs.com/123789456ye/p/12739149.html)

本题中运用的“最优构造”思想是很常用的，在不会影响答案的情况下贪心的取。

另外显然并不会重复计算某个 k

``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 2e5 + 10;
int n, m, tot, f[N], vis[N], p[N], ans;

int main() {
    cin >> n >> m;
    rep(i, 2, n) {
        if (!vis[i]) p[++tot] = i;
        for (int j = i; j <= n; j += i) vis[j] = 1;
    }
    f[0] = 1;
    rep(i, 1, tot)
        for (int j = n; j >= p[i]; j--) {
            int tmp = p[i];
            while (tmp <= j) {
                f[j] = (f[j] + 1ll * f[j - tmp] * tmp % m) % m;
                tmp *= p[i];
            }
        }
    rep(i, 0, n) ans = (ans + f[i]) % m;
    printf("%d\n", ans);
    return 0;
}
```