---
title: 'Usaco19Dec (Gold)'
date: 2019-12-21 21:30:40
tags: 
    - 比赛
mathjax: true
---

[传送门](http://www.usaco.org/index.php?page=dec19results)

Usaco Gold round 第一次！！做出了 T1，个人感觉 usaco 题目质量十分的高啊~ 都不是毒瘤题，但本人过于菜鸡了 qaq

### A. Milk Pumping
-----

一句话题解：最短路变形。

以前好像做过类似的。只要按照边的流量从大到小排序，一一加边，每加一次跑一遍最短路即可！

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

typedef long long ll;
const int N = 1e3 + 10, inf = 0x3f3f3f3f;
int n, m, dis[N], inq[N], ansx, ansy;
struct edge { int x, y, c, f; }e[N];
ll cur;
vector<edge> nxt[N];
queue<int> q;

bool cmp(edge a, edge b) { return a.f > b.f; }

void spfa() {
    rep(i, 1, n) dis[i] = inf;
    dis[1] = 0;
    q.push(1);
    while (q.size()) {
        int x = q.front(); q.pop(); inq[x] = 0;
        for (int i = 0; i < nxt[x].size(); i++) {
            edge now = nxt[x][i];
            if (dis[now.y] > dis[x] + now.c) {
                dis[now.y] = dis[x] + now.c;
                if (!inq[now.y]) inq[now.y] = 1, q.push(now.y);
            }
        }
    }
}

int main() {
    freopen("pump.in", "r", stdin);
    freopen("pump.out", "w", stdout);

    cin >> n >> m;
    rep(i, 1, m)
        scanf("%d%d%d%d", &e[i].x, &e[i].y, &e[i].c, &e[i].f);
    sort(e + 1, e + m + 1, cmp);

    cur = -1e18;
    rep(i, 1, m) {
        nxt[e[i].x].push_back(e[i]);
        swap(e[i].x, e[i].y);
        nxt[e[i].x].push_back(e[i]);
        spfa();
        if (dis[n] != inf)
            cur = max(cur, (1ll * 1000000 * e[i].f / dis[n]));
    }
    printf("%lld\n", cur);
    return 0;
}
```

### B. Milk Visits
-----

很妙的题！首先把询问离线。对于 (x, y, c) 这组询问（设 lca(x, y) = L）显然可以分解为 x ~ L 这段和 y ~ L 这段。

dfs。用栈 (实际操作可用vector) 储存经过的点的颜色和点的位置（实际操作储存深度），到达点 x 时看看栈中是否有 c 颜色的，以及离 x 最近的（深度最大的）点是不是 L 的祖先，如果不是，(x, y, c) 这个询问的答案就是 1.

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
#define fi first
#define se second
using namespace std;

typedef pair<int, int> pii;
const int N = 1e5 + 10;
int n, m, col[N], id[N][2], c[N], cur, ok[N];
pii range[N];
vector<pii> stor[N];
vector<int> todo[N], v[N], ord;

void pre(int x, int fa) {
    range[x].fi = ++cur;
    for (int i = 0; i < v[x].size(); i++)
        if (v[x][i] != fa) pre(v[x][i], x);
    range[x].se = cur;
}

bool anc(int x, int y) {  // is_ancestor ?
    return (range[x].fi <= range[y].fi && range[y].se <= range[x].se);
}

void dfs(int x, int fa) {
    stor[col[x]].push_back(make_pair(x, ord.size()));
    ord.push_back(x);
    for (int o = 0; o < todo[x].size(); o++) {
        int i = todo[x][o];
        if (stor[c[i]].size()) {
            pii y = stor[c[i]].back();
            if (y.fi == x) ok[i] = 1;
            else {
                int son = ord[y.se + 1];  // 下一个 (想想为什么)
			    // x is one of endpoints for query t
                if (!anc(son, id[i][0] + id[i][1] - x)) ok[i] = 1;
            }
        }
    }
    for (int i = 0; i < v[x].size(); i++)
        if (v[x][i] != fa) dfs(v[x][i], x);
    stor[col[x]].pop_back();
    ord.pop_back();
}

int main() {
    freopen("milkvisits.in", "r", stdin);
    freopen("milkvisits.out", "w", stdout);

    cin >> n >> m;
    rep(i, 1, n) scanf("%d", &col[i]);
    rep(i, 1, n - 1) {
        int x, y; scanf("%d%d", &x, &y);
        v[x].push_back(y), v[y].push_back(x);
    }
    pre(1, 0);
    rep(i, 1, m) {
        scanf("%d%d%d", &id[i][0], &id[i][1], &c[i]);
        rep(j, 0, 1) todo[id[i][j]].push_back(i);
    }
    dfs(1, 0);
    rep(i, 1, m) printf("%d", ok[i] ? 1 : 0); puts("");
    return 0;
}
```

### C. Moortal Cowmbat
-----

DP 大法好....

DP 还是弱啊。。其实不是很难

首先跑一遍 Floyd！。设 f[i] 表示前 i 个合法的最小值。

f[i] = min(f[j] + calc(j + 1, i, c)) (j <= i - k)，其中 calc(l, r, c) 表示区间 [l, r] 全变为 c 的价值和。

发现每次只加进去 i - k 的贡献。用 mn[j] 数组维护当前取 j 颜色且合法的最小值，O(1)。

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 1e5 + 10;
int n, m, K;
int c[30][30], cost[N][30], mn[N], f[N];
char s[N];

int calc(int l, int r, int c) { return cost[r][c] - cost[l - 1][c]; }

int main() {
    freopen("cowmbat.in", "r", stdin);
    freopen("cowmbat.out", "w", stdout);

    cin >> n >> m >> K;
    --m;
    scanf("%s", s + 1);
    rep(i, 0, m)
        rep(j, 0, m)
            cin >> c[i][j];
    rep(k, 0, m)
        rep(i, 0, m)
            rep(j, 0, m)
                c[i][j] = min(c[i][j], c[i][k] + c[k][j]);
    rep(i, 0, m)
        rep(j, 1, n)
            cost[j][i] = cost[j - 1][i] + c[s[j] - 'a'][i];

    memset(mn, 0x3f, sizeof(mn));
    memset(f, 0x3f, sizeof(f));
    f[0] = 0;
    rep(i, K, n)
        rep(j, 0, m) {
            mn[j] = min(mn[j] + c[s[i] - 'a'][j], f[i - K] + calc(i - K + 1, i, j));  // mn[j] 表示当前 i 位置为 j 时的最小花费
            f[i] = min(f[i], mn[j]);
        }
    printf("%d\n", f[n]);
    return 0;
}
```

要多打比赛！等考完 看我不屠它个百来道题 (♯｀∧´) 哼