---
title: 'Boruvka算法学习笔记'
date: 2020-08-01 10:12:40
tags: 
    - 最小生成树
mathjax: true
---

$Boruvka..Bvrouka...Brouvka....???$

$Boruvka$ 是一个古老的最小生成树算法，综合了 $Kruskal$ 和 $Prim$.（我觉得更像是进阶版 $Prim$）

它的算法流程是：初始时每个节点都是一个连通块，每次从**每个**块伸出去一条最短边，然后把新形成的连通块缩成一个新的块。每次 块个数至少减半，所以时间复杂度是 log 的。算法正确性可以参考 $Prim$ 的，相当于每次扩展多棵子树。

B **合并只有 log 次**，这是 K 和 P 做不到的。

通常它作为一种**思想**会很有用，比如**位运算生成树**，通常是边权难以直接维护的。

## $[CF888G]$
-----

题意：边 $<i, j>$ 边权为 $a_i\ XOR\ a_j$ 的最小生成树

考虑 $Trie$ 树，共有 $n - 1$ 个 $LCA$（$n - 1$ 个有两个儿子的节点）

根据 B 算法的**思想**，（将每个子树视为一个块），容易发现若 $x$ 同时有 $0/1$ 子树，两个子树就会连边

遍历 $Trie$ 树，在每个 $LCA$ 处做启发式合并、查左右子树连边的最小值

## $[CF1305G]$
-----

题意：连边 $<i, j>$ 规则为 $a_i\ AND\ a_j = 0$ 的最大生成树

注意到这是一棵树，且它的特性在于，每个点的入度仅为 1.

于是有个很妙的方法：将每条边权值改为 $a_i + a_j$，最终答案是边权和 $- \sum{a_i}$，那么答案就转化成最大生成树。

$boruvka$ 算法直接做，每一轮子集 dp 维护出每个二进制集合内最大和次大的点权，**两个点所在集合不同**，这样查询的时候查询补集，就算最大的点权和当前点在同一集合里，也能“撞”掉，选到次大点。可以用 pair 记录 <点权，集合的id>。

``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
#define fi first
#define se second
using namespace std;

const int N = 3e5 + 10, M = (1 << 18);
typedef long long ll;
typedef pair<int, int> pii;
int n, a[N], fa[N], cnt;
ll ans;
pii mx[M][2], cur[M];

int getfa(int x) {
    return fa[x] == x ? x : fa[x] = getfa(fa[x]);
}

bool merge(int x, int y) {
    x = getfa(x), y = getfa(y);
    if (x == y) return 0;
    fa[x] = y;
    return 1;
}

int main() {
    cin >> n;
    rep(i, 1, n) {
        scanf("%d", &a[i]), fa[i] = i;
        ans -= a[i];
    }
    cnt = n + 1;
    while (cnt > 1) {
        rep(i, 0, M - 1)
            rep(j, 0, 1)
                mx[i][j] = make_pair(-1, -1);
        rep(i, 0, n) {
            pii t = make_pair(a[i], getfa(i));
            if (mx[a[i]][0] < t) {
                if (mx[a[i]][0].se != t.se) mx[a[i]][1] = mx[a[i]][0];
                mx[a[i]][0] = t;
            } else if (mx[a[i]][1] < t) {
                if (mx[a[i]][0].se != t.se) mx[a[i]][1] = t;
            }
        }
        rep(i, 0, M - 1) {
            rep(j, 0, 18) {
                if (i & (1 << j)) {
                    rep(k, 0, 1) {
                        if (mx[i][0] < mx[i ^ (1 << j)][k]) {
                            if (mx[i][0].se != mx[i ^ (1 << j)][k].se) mx[i][1] = mx[i][0];
                            mx[i][0] = mx[i ^ (1 << j)][k];
                        } else if (mx[i][1] < mx[i ^ (1 << j)][k]) {
                            if (mx[i][0].se != mx[i ^ (1 << j)][k].se) mx[i][1] = mx[i ^ (1 << j)][k];
                        }
                    }
                }
            }
        }
        rep(i, 0, n)
            cur[i] = make_pair(-1, -1);
        rep(i, 0, n) {
            int f = getfa(i);
            int t = ((M - 1) ^ a[i]);
            if (mx[t][0].se != -1 && mx[t][0].se != f) {
                cur[f] = max(cur[f], make_pair(mx[t][0].fi + a[i], mx[t][0].se));
            } else if (mx[t][1].se != -1 && mx[t][1].se != f) {
                cur[f] = max(cur[f], make_pair(mx[t][1].fi + a[i], mx[t][1].se));
            }
        }
        rep(i, 0, n) {
            if (fa[i] == i && merge(i, cur[i].se)) {
                cnt--, ans += cur[i].fi;
            }
        }
    }
    printf("%lld\n", ans);
    return 0;
}
```

$O(2^{18} * 18 * logn)$

## $[UOJ176]$
-----

题意：边 $<i, j>$ 边权为 $a_i\ AND\ a_j$ 的最大生成树

通过 cf1305g，我们积累了一定的经验。直接想到 B 算法，现在的问题就是怎么维护 a & b 最大。

枚举子集的话应该可以做，不过是 3^18 的？

再想想 trie 树，我们发现跟 XOR 不同的是，1 的话走 1，0 的话走 0/1 皆可，可这样的复杂度是不对的啊，$O(n*2^m*logn)$ ！

这时只要**自底向上**（不然会出问题）将 1 子树合并到 0 子树上就好了！就变成了 1 走 1，0 走 0！复杂度就是 $O((n + 2^m) * mlogn)$

实现细节跟前一题一样，保留两个最大的 id，保证“撞”掉还有，查询的时候要求每时每刻走到的子树都有 和当前点不在同一集合的点

``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
#define fi first
#define se second
using namespace std;

const int N = 3e5 + 5, M = 8e6 + 5, inf = 0x3f3f3f3f;  // 无语子😓 M 开 2 << 18 就 MLE，开 8e6 就 AC。。。
typedef long long ll;
typedef pair<int, int> pii;
int n, m, cnt, idx, rt, cas;
int a[N], fa[N], ch[M][2], mx[M][2];
ll ans;
pii cur[N];

int getfa(int x) {
    return fa[x] == x ? x : fa[x] = getfa(fa[x]);
}

void upd(int x, int y) {
    if (!x || !y) return;
    if (mx[x][0] == mx[y][0]) mx[x][1] = max(mx[x][1], mx[y][1]);
    if (mx[x][0] < mx[y][0]) mx[x][1] = max(mx[x][0], mx[y][1]);
    if (mx[x][0] > mx[y][0]) mx[x][1] = max(mx[x][1], mx[y][0]);
    mx[x][0] = max(mx[x][0], mx[y][0]);
}

int newnode() {
    ++idx;
    ch[idx][0] = ch[idx][1] = 0, mx[idx][0] = mx[idx][1] = -1;
    return idx;
}

int merge(int x, int y) {
    if (!x || !y) return x | y;
    int t = newnode();
    ch[t][0] = merge(ch[x][0], ch[y][0]);
    ch[t][1] = merge(ch[x][1], ch[y][1]);
    upd(t, x), upd(t, y);
    return t;
}

void dfs(int x, int dep) {
    if (dep < 0) return;
    int ls = ch[x][0], rs = ch[x][1];
    if (ls) dfs(ls, dep - 1);
    if (rs) dfs(rs, dep - 1);
    ch[x][0] = merge(ch[x][0], ch[x][1]);
}

bool unite(int x, int y) {
    x = getfa(x), y = getfa(y);
    if (x == y) return 0;
    fa[x] = y;
    return 1;
}

void insert(int &x, int dep, int val, int id) {
    if (!x) x = ++idx;
    if (dep < 0) {
        if (mx[x][0] != id) {
            if (mx[x][0] < id) mx[x][1] = mx[x][0], mx[x][0] = id;
            else if (mx[x][1] < id) mx[x][1] = id;
        }
        return;
    }
    int c = ((val >> dep) & 1);
    insert(ch[x][c], dep - 1, val, id);
    mx[x][0] = mx[x][1] = -1;
    upd(x, ch[x][0]), upd(x, ch[x][1]);
}

pii query(int x, int dep, int val, int id) {
    if (dep < 0) {
        return make_pair(0, (id == mx[x][0] ? mx[x][1] : mx[x][0]));
    }
    int c = ((val >> dep) & 1), ls = ch[x][0], rs = ch[x][1];
    if (c) {
        if (!rs || (id == mx[rs][0] && mx[rs][1] == -1)) {
            return query(ls, dep - 1, val, id);
        } else {
            pii t = query(rs, dep - 1, val, id);
            t.fi += (1 << dep);
            return t;
        }
    } else {
        return query(ls, dep - 1, val, id);
    }
}

int main() {
    cin >> n >> m;
    rep(i, 1, n) {
        scanf("%d", &a[i]);
        fa[i] = i;
    }
    cnt = n;
    while (cnt > 1) {
        ++cas;
        rep(i, 0, idx) {
            ch[i][0] = ch[i][1] = 0;
            mx[i][0] = mx[i][1] = -1;
        }
        idx = rt = 0;
        rep(i, 1, n) {
            insert(rt, m, a[i], getfa(i));
            cur[i] = make_pair(-1, -1);
        }
        dfs(rt, m);
        rep(i, 1, n) {
            int f = getfa(i);
            pii t = query(rt, m, a[i], f);
            cur[f] = max(cur[f], t);
        }
        rep(i, 1, n) {
            if (fa[i] == i && cur[i].se > 0 && unite(i, cur[i].se)) {
                cnt--, ans += cur[i].fi;
            }
        }
    }
    printf("%lld\n", ans);
    return 0;
}
```