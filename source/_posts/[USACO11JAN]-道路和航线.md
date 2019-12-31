---
title: '[USACO11JAN]-道路和航线'
date: 2019-12-31 22:13:11
tags:
    - 最短路
    - 拓扑排序
mathjax: true
---

[传送门](https://www.lydsy.com/JudgeOnline/problem.php?id=2200)

这题啊 诶，USACO 早年比赛数据水，SPFA SLF 优化就可以过（复习一波！

code :
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 25005, INF = (int)1e9;
int T, R, P, S, dis[N];
bool inq[N];
vector<int> nxt[N], val[N];
deque<int> q;  // SLF优化，双端队列

void spfa() {
    for (int i = 1; i <= T; i++) dis[i] = INF;
    q.push_back(S);
    dis[S] = 0;
    inq[S] = 1;
    while (!q.empty()) {
        int u = q.front();
        q.pop_front();
        inq[u] = 0;
        for (int i = 0; i < (int)nxt[u].size(); i++) {
            if (dis[nxt[u][i]] > dis[u] + val[u][i]) {
                dis[nxt[u][i]] = dis[u] + val[u][i];
                if (inq[nxt[u][i]]) continue;
                inq[nxt[u][i]] = 1;
                if (!q.empty()) {
                    if (dis[nxt[u][i]] < dis[q.front()]) q.push_front(nxt[u][i]);
                    else q.push_back(nxt[u][i]);
                }
                else q.push_front(nxt[u][i]);  // q.push_back(nxt[u][i]) 也可以
            }
        }
    }
}

int main() {
    ios::sync_with_stdio(false);
    cin >> T >> R >> P >> S;
    for (int i = 1; i <= R; i++) {
        int u, v, w;
        cin >> u >> v >> w;
        nxt[u].push_back(v);
        nxt[v].push_back(u);
        val[u].push_back(w);
        val[v].push_back(w);
    }
    for (int i = 1; i <= P; i++) {
        int u, v, w;
        cin >> u >> v >> w;
        nxt[u].push_back(v);
        val[u].push_back(w);
    }
    spfa();
    for (int i = 1; i <= T; i++) {
        if (dis[i] == INF) printf("NO PATH\n");
        else printf("%d\n", dis[i]);
    }
    return 0;
}
```

当然要写一波正解啦！

我们发现，将由双向边构成的强连通分量（看作有向图的话）缩点，然后是一个 DAG，显然可以拓扑，然后对于每一个强连通分量跑最短路（题目有保证双向边 val > 0，dijkstra是首选 因为稳！！！）

时间复杂度分析：每个大小 r 的团跑 dij 为 r log r，那么设 R = sigma(r)，就是 R log R, 然后拓扑是 O(T + P), 所以总的是 O(T + P + R log R)，这个注意！！

写的话，还是有一些细节的，看代码：

``` c++
// O(T + P + R log R)!!
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
#define fi first
#define se second
using namespace std;

typedef pair<int, int> pii;
const int N = 5e4 + 10, inf = 0x3f3f3f3f;
int n, m, p, S, c;
int col[N], deg[N], dis[N], vis[N];
int lnk[N], nxt[N << 2], to[N << 2], cnt, val[N << 2];
vector<int> v[N];
vector<pii> plane[N];
queue<int> q;
priority_queue<pii, vector<pii>, greater<pii> > pq;

void add(int x, int y, int z) {
    to[++cnt] = y, nxt[cnt] = lnk[x], lnk[x] = cnt, val[cnt] = z;
}

void dfs(int x, int num) {
    v[num].push_back(x);
    col[x] = num;
    for (int i = lnk[x]; i; i = nxt[i]) {
        int y = to[i];
        if (!col[y]) dfs(y, num);
    }
}

void dijkstra() {
    while (pq.size()) {
        pii u = pq.top(); pq.pop();
        int x = u.se;
        if (dis[x] != u.fi) continue;
        for (int i = lnk[x]; i; i = nxt[i]) {
            int y = to[i], z = val[i];
            if (dis[x] < inf && dis[y] > dis[x] + z) {
                dis[y] = dis[x] + z;
                pq.push(make_pair(dis[y], y));
            }
        }
        for (int i = 0; i < plane[x].size(); i++) {
            int y = plane[x][i].fi, z = plane[x][i].se;
            if (dis[x] < inf && dis[y] > dis[x] + z) dis[y] = dis[x] + z;
            if (!(--deg[col[y]])) q.push(col[y]);
        }
    }
}

void solve() {
    memset(dis, 0x3f, sizeof(dis));
    dis[S] = 0;
    while (q.size()) {
        int now = q.front(); q.pop();
        for (int i = 0; i < v[now].size(); i++)
            pq.push(make_pair(dis[v[now][i]], v[now][i]));
        dijkstra();
    }
}

int main() {
    cin >> n >> m >> p >> S;
    rep(i, 1, m) {
        int a, b, c; scanf("%d%d%d", &a, &b, &c);
        add(a, b, c), add(b, a, c);
    }
    rep(i, 1, n) if (!col[i]) dfs(i, ++c);
    rep(i, 1, p) {
        int a, b, c; scanf("%d%d%d", &a, &b, &c);
        plane[a].push_back(make_pair(b, c));
        deg[col[b]]++;  // 题目中保证了 col[a] != col[b]
    }
    // q.push(col[S]);  !!! important 加了就会出现：团 col[S] 指向的团 y 被重复减 deg 了，y 提前入 queue，会错过更优解
    rep(i, 1, c) if (!deg[i]) q.push(i);  // 度数为 0 的团要加的：团 a -> col[S] -> b, a -> b，若不加 a 则 b 的 deg 不会为 0
    solve();
    rep(i, 1, n) {
        if (dis[i] == inf) puts("NO PATH");
        else printf("%d\n", dis[i]);
    }
    return 0;
}
```

插一句题外话：木木写了题解，有些有趣的东西，就是按照拓扑序为顺序跑 dij。区别在于：我的是边拓扑边决定下一个团跑哪个，他的是事先就决定了顺序。（虽然这题没啥用 QAQ