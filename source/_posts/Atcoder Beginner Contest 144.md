---
title: 'Atcoder Beginner Contest 144'
date: 2019-10-30 12:10:40
tags: 
    - 比赛
mathjax: true
---

[传送门](https://atcoder.jp/contests/abc144)

### D - Water Bottle
-----

分类讨论 + 三角函数

正切是我们平时说的直线的斜率，反正切（c++中的atan函数）的意义大概是这样的：若 A 为角，tanA = x / y, 则 A = arctan x / y. 而 c++ 中计算使用的是弧度制，所得的要转化为角度，还需乘以 180 / PI.

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const double PI = 3.1415926535897923;
double a, b, x, v, t;

int main() {
    cin >> a >> b >> x;
    v = a * a * b;
    if (x >= v / 2) {
        t = 2 * (v - x) / (a * a);
        printf("%.7lf\n", atan(t / a) * 180 / PI);
    } else {
        t = 2 * x / a / b;
        printf("%.7lf\n", 90 - atan(t / b) * 180 / PI);
    }
    return 0;
}
```

### E - Gluttony
-----

贪心 + 二分

根据贪心策略很容易想到：将 A 从小到大排，F 从大到小排，Ai 与 Fi 配。然后本题的实质就变成了“最大值最小”，二分妥妥的！

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

typedef long long ll;
const int N = 2e5 + 10;
ll n, K, a[N], f[N], l, r, ans;

bool cmp(ll a, ll b) {
    return a > b;
}

bool chk(ll lim) {
    ll cnt = 0;
    rep(i, 1, n)
        cnt += max(0ll, a[i] - lim / f[i]);
    return cnt <= K;
}

int main() {
    cin >> n >> K;
    rep(i, 1, n) scanf("%lld", &a[i]);
    rep(i, 1, n) scanf("%lld", &f[i]);
    sort(a + 1, a + n + 1);
    sort(f + 1, f + n + 1, cmp);
    rep(i, 1, n) r = max(r, a[i] * f[i]);

    while (l <= r) {
        ll mid = (l + r) >> 1;
        if (chk(mid)) r = mid - 1, ans = mid;
        else l = mid + 1;
    }
    printf("%lld\n", ans);
    return 0;
}
// 最大值最小！最大值最小！最大值最小！重要的事情汪三遍
```

### F - Fork in the Road
-----

一开始思路是枚举断边，每次拓扑一遍计算 f[x] 表示 x 到 n 的期望步数，这样复杂度是 $O(m(n + m))$ 的，不太ok。

注意到 m 挺大，n 比较小，我们可以枚举断哪个点 x 周围的边（假设该边连接 x 和 y，必然断的是 f[y] 最大的边），复杂度是 $O(n(n+m))$ 的，可以切了此题。

code :
``` c++
// si < ti !!! si < ti !!! si < ti !!! 条件还能漏的啊？你眼睛呢？？！
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 605;
const double inf = (double)0x3f3f3f3f;
int n, m, sons;
bool vis[N];
double ans, f[N];
struct edge { int s, t; }e[N * N];
vector<int> nxt[N];

double calc(int pos) {
    rep(i, 1, n) f[i] = 0;
    for (int x = n - 1; x >= 1; x--) {
        int num = nxt[x].size();
        if (pos == x && num == 1) { f[x] = inf; continue; }
        double t = 1.0 / (num - (pos == x)), mx = 0;
        for (int i = 0; i < num; i++) {
            int y = nxt[x][i];
            f[x] += (f[y] + 1) * t;
            mx = max(mx, (f[y] + 1) * t);
        }
        if (pos == x) f[x] -= mx;
    }
    return f[1];
}

int main() {
    cin >> n >> m;
    rep(i, 1, m) {
        scanf("%d%d", &e[i].s, &e[i].t);
        nxt[e[i].s].push_back(e[i].t);
        if (e[i].s == 1) sons++;
    }
    ans = calc(0);

    rep(i, 1, n) ans = min(ans, calc(i));
    printf("%.7lf\n", ans);
    return 0;
}
```