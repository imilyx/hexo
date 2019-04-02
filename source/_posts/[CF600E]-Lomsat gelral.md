---
title: '[CF600E]-Lomsat gelral'
date: 2019-04-02 23:21:40
tags: 
    - 树上启发式合并
    - 线段树合并
mathjax: true
---

[题目链接](http://codeforces.com/contest/600/problem/E)

大概意思就是让你求每个子树内颜色数最多的颜色总和（有点绕。。

其实是个模版题！我先用树上启发式合并 (dsu on tree) 过的。

树上启发式合并其实就是暴力的优化版！大概步骤为：

处理轻儿子(清除轻儿子信息) -> 处理重儿子(不清除重儿子信息) -> 再加入轻儿子信息，计算父亲信息 -> 如果当前父亲为祖父的轻儿子，清除父亲及其子树信息.

其实就是将 size 较小的（此处体现为轻儿子）合并到 size 较大的（此处体现为重儿子）上，从而达到降低复杂度的效果。如何分析复杂度呢？一个轻儿子子树里的点至多会被消除 logN 次（因为这点上面最多有 logN 条边），利用轻重链剖分的思想，就能 O(nlogn) 解决问题。（此处假设合并信息是 O(1) 的）

code :
``` c++
// dsu on tree
#include <bits/stdc++.h>
using namespace std;

const int N = 1e5 + 10;
typedef long long ll;
int n, mx;
int a[N], f[N], sz[N], son[N], vis[N];
int to[N << 1], nxt[N << 1], lnk[N], tot;
ll sum, ans[N], cnt[N];

void add(int x, int y) {
    to[++tot] = y, nxt[tot] = lnk[x], lnk[x] = tot;
}

void dfs(int x, int fa) {
    f[x] = fa;
    sz[x] = 1;
    for (int i = lnk[x]; i; i = nxt[i]) {
        int y = to[i];
        if (y == fa) continue;
        dfs(y, x);
        sz[x] += sz[y];
        if (sz[y] > sz[son[x]]) son[x] = y;
    }
}

void calc(int x, int val) {
    cnt[a[x]] += val;
    if (cnt[a[x]] == mx) sum += a[x];
    else if (cnt[a[x]] > mx) sum = a[x], mx = cnt[a[x]];
    for (int i = lnk[x]; i; i = nxt[i])
        if (to[i] != f[x] && !vis[to[i]])
            calc(to[i], val);
}

void dfs2(int x) {
    for (int i = lnk[x]; i; i = nxt[i]) {
        int y = to[i];
        if (y != f[x] && y != son[x]) dfs2(y);
    }
    if (son[x]) dfs2(son[x]), vis[son[x]] = 1;
    calc(x, 1);
    ans[x] = sum;
    if (son[x]) // = if (son[x] && son[f[x]] != x)
        vis[son[x]] = 0;
    if (son[f[x]] != x) calc(x, -1), sum = 0, mx = 0;
}

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d", &a[i]);
    for (int i = 1; i < n; i++) {
        int x, y; scanf("%d%d", &x, &y);
        add(x, y), add(y, x);
    }
    dfs(1, 0);
    dfs2(1);
    for (int i = 1; i <= n; i++) printf("%lld ", ans[i]);
    puts("");
    return 0;
}
```

还有一种方法：线段树合并。顾名思义，也是一种合并。这是在每个节点上开一棵线段树，每次将儿子的线段树合并到父亲的线段树里。假设合并操作是 logN 的。线段树合并复杂度取决于重合节点个数，而现实中一般重合较少，可以视为 logN，因此复杂度嘛，，O(nlogn)?

code :
``` c++
// 线段树合并
#include <bits/stdc++.h>
using namespace std;

const int N = 1e5 * 20;
typedef long long ll;
int n, c[N], id;
int to[N], nxt[N], lnk[N], tot;
int ls[N], rs[N], mx[N], rt[N];
ll sum[N], ans[N];

void add(int x, int y) {
    to[++tot] = y, nxt[tot] = lnk[x], lnk[x] = tot;
}

void insert(int &x, int l, int r, int val) {
    x = ++id;
    sum[x] = val, mx[x] = 1;
    if (l == r) return;
    int mid = (l + r) >> 1;
    if (val <= mid) insert(ls[x], l, mid, val);
    else insert(rs[x], mid + 1, r, val);
}

void upd(int x) {
    int l = ls[x], r = rs[x];
    mx[x] = mx[l], sum[x] = sum[l];
    if (mx[r] == mx[x]) sum[x] += sum[r];
    else if (mx[r] > mx[x]) sum[x] = sum[r], mx[x] = mx[r];
}

int merge(int x, int y, int l, int r) {
    if (!x || !y) return x | y;
    if (l == r) { mx[x] += mx[y]; return x; }
    int mid = (l + r) >> 1;
    ls[x] = merge(ls[x], ls[y], l, mid);
    rs[x] = merge(rs[x], rs[y], mid + 1, r);
    upd(x);
    return x;
}

void dfs(int x, int fa) {
    insert(rt[x], 1, n, c[x]);
    for (int i = lnk[x]; i; i = nxt[i]) {
        int y = to[i];
        if (y == fa) continue;
        dfs(y, x);
        rt[x] = merge(rt[x], rt[y], 1, n);
    }
    ans[x] = sum[rt[x]];
}

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d", &c[i]);
    for (int i = 1; i < n; i++) {
        int x, y; scanf("%d%d", &x, &y);
        add(x, y), add(y, x);
    }
    dfs(1, 0);
    for (int i = 1; i <= n; i++) printf("%lld ", ans[i]);
    puts("");
    return 0;
}```

其实这题两种都很好写啊！(x