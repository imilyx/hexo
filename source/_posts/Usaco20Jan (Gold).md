---
title: 'Usaco20Jan (Gold)'
date: 2020-02-02 17:21:40
tags: 
    - 比赛
mathjax: true
---

[传送门](http://www.usaco.org/index.php?page=jan20results)

题都很好，我太菜了，加油！冲冲冲

### A. Time is Mooney
-----

可以发现，点权最大是 1000，C 最小是 1，也就意味着 1000t - t^2 >= 0, t <= 1000!!

那么枚举 t 就可以啦，ans = max(从 1 出发，经过 t 步走回 1 的 maxval)

设 dp[t, i] 表示走 t 步到 i 节点的最大点权和，这样就能求括号里的那个东西啦。。

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 2e3 + 10;
typedef long long ll;
typedef pair<int, int> pii;
ll dp[2][N], n, m, C, a[N], ans;  // 滚动
vector<pii> edges;

int main() {
    freopen("time.in", "r", stdin);
    freopen("time.out", "w", stdout);

    cin >> n >> m >> C;
    rep(i, 1, n) scanf("%lld", &a[i]);
    rep(i, 1, m) {
        int a, b; cin >> a >> b;
        edges.push_back(make_pair(a, b));
    }
    memset(dp, -1, sizeof(dp));
    ans = -1e18;
    dp[0][1] = 0;
    rep(t, 1, 1000) {
        int p = t % 2;
        memset(dp[p], -1, sizeof(dp[p]));
        for (int i = 0; i < edges.size(); i++) {
            int x = edges[i].first, y = edges[i].second;
            if (dp[p ^ 1][x] != -1)
                dp[p][y] = max(dp[p][y], dp[p ^ 1][x] + a[y]);
        }
        ans = max(ans, dp[p][1] - C * t * t);
    }
    printf("%lld\n", ans);
    return 0;
}
```

### B. Farmer John Solves 3SUM
-----

n = 5000，妥妥 n^2。如果能做到 n^2 预处理就好啦！

滴--！用 pbds 库里的 hash，O(1) 超快，用 set 或 map 会超时der。。。

具体用法下面有👇

code :
``` c++
// map, set, multimap, and multiset: log(n)
// hash_map, hash_set, hash_multimap, and hash_multiset: best O(1), worst O(n)
// pb_ds库里的hash: 1. 需要头文件(line 5,6,8) 2. 函数声明(line 23) 3. 使用方式(line 25~28)
// C++11!
// C++11!!
// C++11!!!
#include <bits/stdc++.h>
#include <ext/pb_ds/assoc_container.hpp>
#include <ext/pb_ds/hash_policy.hpp>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace __gnu_pbds;
using namespace std;

const int N = 5e3 + 5;
typedef long long ll;
int n, Q, a[N];
ll ans[N][N];

int main() {
    freopen("threesum.in", "r", stdin);
    freopen("threesum.out", "w", stdout);

    cin >> n >> Q;
    rep(i, 1, n) scanf("%d", &a[i]);
    rep(i, 1, n) {
        gp_hash_table<int, int> g({}, {}, {}, {}, {1 << 13});
        rep(j, i + 1, n) {
            auto it = g.find(-a[i] - a[j]);
            if (it != end(g)) ans[i][j] = it->second;
            g[a[j]]++;
        }
    }
    for (int i = n; i >= 1; i--)
        rep(j, i, n)
            ans[i][j] += ans[i + 1][j] + ans[i][j - 1] - ans[i + 1][j - 1];
            
    while (Q--) {
        int l, r; scanf("%d%d", &l, &r);
        printf("%lld\n", ans[l][r]);
    }
    return 0;
}
```

### C. Springboards
-----

将特殊点按 x、y 坐标排序。设 ans[i] 表示到第 i 个落地点的最短距离。

第 i 个点之前的点是第 j 个的话，ans[i] = xi + yi - xj - yj + ans[j]

所以我们碰到落地点就计算 ans[i], 碰到起跳点就将 ans[i] - xi - yi 加入 map，map 维护之前经过的所有点的答案。map 第一维是 y 坐标，第二维是 val。

显然，对于两个入 map 顺序 i < j 的点，若 yi < yj 且 vali < valj，那么 j 显然没有必要入 map。本着这个原则。显然 map 最后是 y 越大，val 越小的。

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
#define fi first
#define se second
using namespace std;

const int N = 1e5 + 10;
typedef pair<int, int> pii;
int n, p, ans[N];
map<int, int> mp;

void insert(int y, int v) {
    auto it = prev(mp.upper_bound(y));
    if ((*it).se <= v) return;  // (*it).se = it->se
    it++;
    while (it != mp.end() && it->se > v) mp.erase(it++);
    mp[y] = v;
}

int main() {
    freopen("boards.in", "r", stdin);
    freopen("boards.out", "w", stdout);

    cin >> n >> p;
    mp[0] = 0;
    vector<pair<pii, pii>> v;
    rep(i, 1, p) {
        pii a, b;
        cin >> a.fi >> a.se >> b.fi >> b.se;
        v.push_back({a, {i, -1}});
        v.push_back({b, {i, 1}});
    }
    sort(v.begin(), v.end());
    for (int i = 0; i < v.size(); i++) {
        if (v[i].se.se == -1) {
            ans[v[i].se.fi] = v[i].fi.fi + v[i].fi.se + prev(mp.upper_bound(v[i].fi.se))->se;
        } else {
            insert(v[i].fi.se, ans[v[i].se.fi] - v[i].fi.fi - v[i].fi.se);
        }
    }
    printf("%d\n", mp.rbegin()->se + 2 * n);
    return 0;
}
```