---
title: '[POI2015]-LOG'
date: 2019-10-29 18:45:40
tags: 
mathjax: true
---

[传送门](https://www.luogu.org/problem/P3586)

“每次选 a 个正数并将它们都减去 1，判断能否进行 s 次操作” 这一类题有个小技巧：

> 设大于等于 s 有 x 个，判定就转化为 $[ x * s + (小于 s 的数字之和) >= a * s ]$

用树状数组要对 s 离散化即可。

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
#define lowbit(x) ((x) & (-(x)))
using namespace std;

const int N = 1e6 + 5;
typedef long long ll;
ll n, m, C[N], cnt[N], a[N], h[N << 1], num;
struct node { char opt; ll x, y; }q[N];

void add1(ll x, ll v) {
    if (!x) return;
    for (; x < N; x += lowbit(x)) C[x] += v;
}
ll ask1(ll x) {
    if (!x) return 0;
    ll ret = 0;
    for (; x; x -= lowbit(x)) ret += C[x];
    return ret;
}

void add2(ll x, ll v) {
    if (!x) return;
    for (; x < N; x += lowbit(x)) cnt[x] += v;
}
ll ask2(ll x) {
    if (!x) return 0;
    ll ret = 0;
    for (; x; x -= lowbit(x)) ret += cnt[x];
    return ret;
}

bool calc(int c, int s) {
    ll x = ask1(num) - ask1(s - 1);
    return x * h[s] + ask2(s - 1) >= 1ll * c * h[s];
}

int main() {
    cin >> n >> m;
    rep(i, 1, m) {
        cin >> q[i].opt;
        scanf("%lld%lld", &q[i].x, &q[i].y);
        h[++num] = q[i].y;
    }
    h[++num] = 0;
    sort(h + 1, h + num + 1);
    num = unique(h + 1, h + num + 1) - h - 1;
    rep(i, 1, m)
        q[i].y = lower_bound(h + 1, h + num + 1, q[i].y) - h;
    
    rep(i, 1, m) {
        char ch; int x, y;
        ch = q[i].opt, x = q[i].x, y = q[i].y;
        if (ch == 'U') {
            if (a[x])
                add1(a[x], -1), add2(a[x], -h[a[x]]);
            a[x] = y;
            add1(a[x], 1);
            add2(a[x], h[a[x]]);
        } else {
            puts(calc(x, y) ? "TAK" : "NIE");
        }
    }
    return 0;
}
```

类似的题目还有 [ATC143F - Distinct Numbers](https://atcoder.jp/contests/abc143/tasks/abc143_f), 只是将 s 替换为二分值 mid 了。二分做法复杂度是 O(nlog2n), 但这里是利用答案单调性的 O(n) 做法（容易发现，随着 K 的减小，答案只会增加不会减少），然后前缀的预处理还是非常神仙的（从网上贺来的qwq），实在不懂可以模拟输出中间过程（其实也不难理解）

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 3e5 + 10;
typedef long long ll;
ll n, x, a[N], K, cnt[N], sum[N], ans[N];

bool chk(ll k, ll x) { return sum[x] >= k * x; }

int main() {
    cin >> n;
    rep(i, 1, n) cin >> x, ++sum[++cnt[x]];
    rep(i, 1, n) sum[i] += sum[i - 1];
    ll now = 0;
    for (int k = n; k >= 1; k--) {
        while (now < n && chk(k, now + 1)) ++now;
        ans[k] = now;
    }
    rep(i, 1, n) printf("%lld\n", ans[i]);
    return 0;
}
```