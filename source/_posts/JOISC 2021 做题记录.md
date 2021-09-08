---
title: 'JOISC 2021 做题记录'
date: 2021-05-14 10:46:40
tags:
mathjax: true
---

写 JOISC 会不会给 THUSC 加 rp 呢？ε-(´∀｀; )

~~都要退役了还加个球~~

[link](https://loj.ac/p?keyword=joisc%202021)

### 最差记者

基环森林。分开做。

环值相等，缩点。

一棵树。

$dp[x, i]$ 表示 $x$ 取 $i$ 的最大代价，线段树合并（做 min 卷积），最后总的减一下

果然我会㗅的都是套路题 /kk 权当复习线段树合并了。

细节：区间 max，单点加，合并要记录右边最大值

upd: 口胡五分钟，调代码一年系列。

<details>
  <summary>code</summary>
  ``` c++
  #include <bits/stdc++.h>
  #define rep(i, x, y) for (int i = x; i <= y; i++)
  #define per(i, x, y) for (int i = x; i >= y; i--)
  #define vi vector<int>
  #define pb push_back
  #define mkp make_pair
  #define fi first
  #define se second
  #define gc getchar
  #define ll long long
  #define Go(i, x, v) for (int i = 0, x = (i == v.size() ? 0 : v[i]); i < v.size(); i++, x = (i == v.size() ? 0 : v[i]))
  using namespace std;
  template <class T> void rd(T &x) {
    x = 0; char ch = gc(); int f = 1;
    for (; !isdigit(ch); ch = gc()) if (ch == '-') f = -1;
    for (; isdigit(ch); ch = gc()) x = (x << 1) + (x << 3) + ch - '0';
    x *= f;
  }
  template <class T> void cmin(T &x, T y) { x = min(x, y); }
  template <class T> void cmax(T &x, T y) { x = max(x, y); }
  /* ============ Header Template ============ */
  const int N = 4e5 + 10, M = N * 40;
  int n, a[N], h[N], c[N], fa[N], vis[N], rot[N];
  vi id[N], H, cir, g[N];
  ll tot;
  map<int, ll> mp;
  int gfa(int x) {
    return fa[x] == x ? x : fa[x] = gfa(fa[x]);
  }
  namespace SGT {
    #define mid (l + r >> 1)
    int ls[M], rs[M], idx;
    ll mx[M], tag[M];
    void dopsd(int x, ll v) {
      tag[x] += v, mx[x] += v;
    }
    void psd(int x) {
      if (tag[x]) {
        if (ls[x]) dopsd(ls[x], tag[x]);
        if (rs[x]) dopsd(rs[x], tag[x]);
        tag[x] = 0;
      }
    }
    void upd(int x) {
      mx[x] = max(mx[ls[x]], mx[rs[x]]);
    }
    void ins(int &x, int l, int r, int p, ll v) {
      if (!x) x = ++idx;
      if (l == r) {
        cmax(mx[x], v); return;
      } psd(x);
      p <= mid ? ins(ls[x], l, mid, p, v) : ins(rs[x], mid + 1, r, p, v);
      upd(x);
    }
    int merge(int x, int y, int l, int r, ll mxx, ll mxy) {  // 灵魂所在！！1。要记录 r 右边最大值因为是 min 卷积。
      if (!x && !y) return 0;
      if (!x) { dopsd(y, mxx); return y; }
      if (!y) { dopsd(x, mxy); return x; }
      if (l == r) {
        cmax(mxx, mx[x]), cmax(mxy, mx[y]);
        mx[x] = max(mx[x] + mxy, mx[y] + mxx);
        return x;
      }
      // if (!ls[x] && !rs[x]) { cmax(mxx, mx[x]), dopsd(y, mxx); return y; }  // !!!
      // if (!ls[y] && !rs[y]) { cmax(mxy, mx[y]), dopsd(x, mxy); return x; }  // !!!
      psd(x), psd(y);
      ls[x] = merge(ls[x], ls[y], l, mid, max(mxx, mx[rs[x]]), max(mxy, mx[rs[y]]));  // 不用先合并右边就能得到 mx
      rs[x] = merge(rs[x], rs[y], mid + 1, r, mxx, mxy);
      upd(x);
      return x;
    }
    ll qry(int x, int l, int r, int lx, int rx) {
      if (!x || rx < l || lx > r) return 0;
      if (lx <= l && r <= rx) return mx[x];
      psd(x);
      return max(qry(ls[x], l, mid, lx, rx), qry(rs[x], mid + 1, r, lx, rx));
    }
  }; using namespace SGT;
  void dfs(int x) {
    Go(i, y, g[x]) {
      dfs(y);
      rot[x] = merge(rot[x], rot[y], 1, n, 0ll, 0ll);
    }
    ins(rot[x], 1, n, h[x], c[x] + qry(rot[x], 1, n, h[x], n));
  }
  signed main() {
    rd(n);
    rep(i, 1, n) fa[i] = i;
    H.pb(1);
    rep(i, 1, n) {
      rd(a[i]), rd(h[i]), rd(c[i]);
      fa[gfa(i)] = gfa(a[i]);
      tot += c[i];
      H.pb(h[i]);
    }
    sort(H.begin(), H.end());
    H.erase(unique(H.begin(), H.end()), H.end());
    rep(i, 1, n) {
      id[gfa(i)].pb(i);
      h[i] = lower_bound(H.begin(), H.end(), h[i]) - H.begin() + 1;
    }
    ll ans = 0;
    rep(i, 1, n) if (i == gfa(i)) {
      int x = i, clk = 0, rt = 0, l, r;
      mp.clear();
      mp[1] = 0;
      cir.clear();
      while (!vis[x]) {
        vis[x] = ++clk, x = a[x];
      } l = vis[x], r = clk;
      Go(o, x, id[i]) {
        if (vis[x] >= l && vis[x] <= r) cir.pb(x), mp[h[x]] += c[x];
        else g[a[x]].pb(x);
      }
      Go(o, x, cir) {
        Go(j, y, g[x]) {
          dfs(y);
          rt = merge(rt, rot[y], 1, n, 0ll, 0ll);
        }
      }
      ll cur = mx[rt];
      for (map<int, ll>::iterator it = mp.begin(); it != mp.end(); it++) {
        cmax(cur, it -> se + qry(rt, 1, n, it -> fi, n));
      }
      ans += cur;
    }
    printf("%lld\n", tot - ans);
    return 0;
  }
  ```
</details>

### 道路建设

### 饮食区

### 聚会 2

### 活动参观 2

### 保镖

### IOI 热病

### 特技飞行