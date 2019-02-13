---
title: 'Codeforces Round 538 Div2'
date: 2019-02-11 21:30:40
tags: 
    - 比赛
mathjax: true
---

[比赛链接](http://codeforces.com/contest/1114)

Div2怎么这么难呀，趋于数学化了 🤔️

### A. Got Any Grapes?
-----

签到题，然而第一次提交把紫葡萄和黑葡萄的顺序看错了。。。成功 -50。。。

### B. Yet Another Array Partitioning Task
-----

这题看着有点眼熟，好像 Global Round 1 的 B 题呀 🤔

一开始想各种贪心，好像都不大可行。其实注意到了 Input 数据范围里有个 $m * k \leq n$, 但不知道有啥用。后来才知道，是因为首先你得选前 $m * k$ 大的吧！这样，你将前 $m * k$ 大的记录下来，每隔 k 个分一组，既满足了每组数量 >= m, 又是最优解。

code :
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 2e5 + 10;
int n, m, k, a[N], mark[N], ret[N], cnt;
typedef long long ll;
ll tot;
struct node {
    int id, num;
}b[N];

bool cmp(node a, node b) { return a.num > b.num; }

int main() {
    cin >> n >> m >> k;
    for (int i = 1; i <= n; i++) {
        scanf("%d", &a[i]);
        b[i].num = a[i], b[i].id = i;
    }
    sort(b + 1, b + n + 1, cmp);
    for (int i = 1; i <= m * k; i++) {
        mark[b[i].id] = 1; tot += b[i].num;
    }
    int sum = 0;
    for (int i = 1; i <= n; i++) {
        if (mark[i]) sum++;
        if (sum == m) ret[++cnt] = i, sum = 0;
    }
    printf("%lld\n", tot);
    for (int i = 1; i < cnt; i++) printf("%d ", ret[i]);
    puts("");
    return 0;
}
```

### C. Trailing Loves (or L'oeufs?)
-----

啊！这是个玄学题！

首先我们来看看 10 进制：10 进制下求 n! 末尾 0 的个数很容易吧，找出 1 ～ n 中 2 和 5 作为因子的个数 a 和 b，答案就是 min(a, b).

由此我们可以大胆猜想，b 进制下求 n! 末尾 0 的个数是同样做法。我们把 b 写成 $b = p_1^{a_1} * p_2^{a_2} * ... * p_k^{a_k}$ 的形式.

众所周知，1 ～ n 中 p 的倍数的个数为 n / p 下取整。这里我们要找出对于每一个 p，它作为因子的个数，即 f(n, p). f(n, p) = n / p + n / (p ^ 2) + n / (p ^ 3). 注意求 f(n, p) 的时候不能先算 (p ^ k), 而应该用不断除的形式，避免精度误差，这一段下面代码中有具体方法。

f(n, pi) 记入答案时要除以 ai, 这个自己理解一下就可以。

code :
``` c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
ll n, b, ans;
ll ok[1000010], prime[1000010], cnt;

void Is() {
    for (int i = 2; i <= 1000000; i++) {
        if (!ok[i]) prime[++cnt] = i;
        else continue;
        for (int j = 2; i * j <= 1000000; j++) ok[i * j] = 1;
    }
}

ll f(ll x, ll y) {
    ll tmpN = x, res = 0;
    while (tmpN) tmpN /= y, res += tmpN;
    return res;
}

int main() {
    scanf("%lld%lld", &n, &b);
    Is();
    ans = 1e18;
    ll tmp = b;
    for (int i = 1; i <= cnt; i++) {
        ll p = prime[i];
        if (p > b) break;
        if (b % p) continue;
        ll tot = 0;
        while (tmp % p == 0) tmp /= p, tot++;
        ll now = f(n, p) / tot;
        ans = min(ans, now);
    }
    if (tmp > 1) ans = min(ans, f(n, tmp));
    printf("%lld\n", ans);
    return 0;
}
```

### D. Flood Fill
-----

这个题是区间的问题，肯定不是线性的，这时候应该考虑一下区间DP啦。一想到区间DP就会有想法了！是不是很像烤乐滋打虎  🤔

设 f[i, j, k] 表示区间[i, j]已经是同一个颜色 p 了，其中如果 k = 0，p = c[i], 否则 p = c[j]. 因为一段区间的改变颜色的操作肯定是为了像滚雪球一样扩大同一颜色的区间嘛，所以颜色不是左端点的就是右端点的。

转移看代码。

code :
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 5e3 + 10;
int n, c[N], f[N][N][2];

int dp(int l, int r, int sta) {
    if (l >= r) return 0;
    if (f[l][r][sta] != -1) return f[l][r][sta];
    int res = 1e9;
    if (sta == 0) {
        res = min(res, dp(l + 1, r, 0) + (c[l] != c[l + 1]));
        res = min(res, dp(l + 1, r, 1) + (c[l] != c[r]));
    } else {
        res = min(res, dp(l, r - 1, 0) + (c[r] != c[l]));
        res = min(res, dp(l, r - 1, 1) + (c[r] != c[r - 1]));
    }
    return f[l][r][sta] = res;
}

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d", &c[i]);
    memset(f, -1, sizeof(f));
    printf("%d\n", min(dp(1, n, 0), dp(1, n, 1)));
    return 0;
}
```