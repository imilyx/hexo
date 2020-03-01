---
title: 'Usaco20Feb (Gold)'
date: 2020-03-01 20:21:40
tags: 
    - 比赛
mathjax: true
---

这次的 gold 比前两场都水？！？T2已经离正解很近了唉。。T3就是个大暴力唉。。。。

### A
-----

差分约束，大于小于搞清楚就可以

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 1e5 + 10, inf = 0x3f3f3f3f;
int n, m, C, s[N], dis[N], inq[N];
int to[N << 1], nxt[N << 1], lnk[N], val[N << 1], cnt;
queue<int> q;

void add(int x, int y, int z) {
    to[++cnt] = y, nxt[cnt] = lnk[x], lnk[x] = cnt, val[cnt] = z;
}

void spfa() {
    memset(dis, 0x3f, sizeof(dis));
    dis[0] = 0;
    q.push(0);
    while (q.size()) {
        int x = q.front(); q.pop(); inq[x] = 0;
        for (int i = lnk[x]; i; i = nxt[i]) {
            int y = to[i];
            if (dis[y] > dis[x] + val[i]) {
                dis[y] = dis[x] + val[i];
                if (!inq[y]) inq[y] = 1, q.push(y);
            }
        }
    }
}

int main() {
    freopen("timeline.in", "r", stdin); freopen("timeline.out", "w", stdout);
    
    cin >> n >> m >> C;
    rep(i, 1, n) scanf("%d", &s[i]), add(0, i, -s[i]);
    rep(i, 1, C) {
        int a, b, x; scanf("%d%d%d", &a, &b, &x);
        add(a, b, -x);
    }
    spfa();
    rep(i, 1, n) printf("%d\n", -dis[i]);
    return 0;
}
```

### B
-----

赛时想了个 DP，最后发现少考虑一种情况。。

但赛后才知道加上那个情况就可以不用 DP 这么麻烦了！！！

不过口胡了一个与此题无关的、算区间内区间的 O(nlogn) 算法。。也算小小小有收获了！！！

code :
``` c++
/*
单独算每个区间 [l, r] 的贡献（就是 2^{n - 1 - 不包含[l - 1, l]的区间数}）
赛时降智。。
*/
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 2e5 + 10, mod = 1e9 + 7;
int n, cnt[N], mi[N] = {1}, ans;
struct line { int l, r; }e[N];

int main() {
    freopen("help.in", "r", stdin); freopen("help.out", "w", stdout);
    cin >> n;
    rep(i, 1, n) cin >> e[i].l >> e[i].r, cnt[e[i].l]++, cnt[e[i].r]--;
    rep(i, 1, 2 * n) cnt[i] += cnt[i - 1], mi[i] = mi[i - 1] * 2 % mod;
    rep(i, 1, n) ans = (ans + mi[n - 1 - cnt[e[i].l - 1]]) % mod;
    printf("%d\n", ans);
    return 0;
}
```

### C
-----

一个大暴力，没啥好说_(´ཀ`」 ∠)_ 怪我太菜。。

code :
``` c++
/*
正常每次dfs，递归常数大。。写checker⬇️就可以
*/
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 1e5 + 10;
int n, sz[N];
vector<int> nxt[N], num[N];

void dfs(int x, int fa) {
    sz[x] = 1;
    for (int i = 0; i < nxt[x].size(); i++) {
        int y = nxt[x][i];
        if (y == fa) continue;
        dfs(y, x);
        sz[x] += sz[y];
        num[x].push_back(sz[y]);
    }
    if (x != 1) num[x].push_back(n - sz[x]);
}

int bin[N];
int chk(int k) {
    if ((n - 1) % k) return 0;
    rep(i, 1, k - 1) bin[i] = 0;
    rep(i, 1, n) {
        int cnt = 0;
        for (int j = 0; j < num[i].size(); j++) {
            int x = num[i][j] % k;
            if (!x) continue;
            if (bin[k - x]) bin[k - x]--, cnt--;
            else bin[x]++, cnt++;
        }
        if (cnt) return 0;
    } return 1;
}

int main() {
    freopen("deleg.in", "r", stdin); freopen("deleg.out", "w", stdout);
    cin >> n;
    rep(i, 1, n - 1) {
        int x, y; scanf("%d%d", &x, &y);
        nxt[x].push_back(y), nxt[y].push_back(x);
    }
    dfs(1, 0);
    rep(i, 1, n - 1) printf("%d", chk(i)); puts("");
    return 0;
}
```