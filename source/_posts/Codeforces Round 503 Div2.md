---
title: 'Codeforces Round 503 Div2'
date: 2018-08-12 22:36:40
tags: 
    - 比赛
mathjax: true
---

http://codeforces.com/contest/1020

### A. New Building for SIS
-----

题意略。

要注意同幢楼的情况！

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
ll n, h, a, b, k, t1, t2, f1, f2;

int main() {
    scanf("%lld%lld%lld%lld%lld", &n, &h, &a, &b, &k);
    while (k--) {
        scanf("%lld%lld%lld%lld", &t1, &f1, &t2, &f2);
        if (f1 > f2) swap(f1, f2);
        if (t1 > t2) swap(t1, t2);
        ll dis = 0;
        if (t1 == t2) {
            dis += (f2 - f1);
        } else {
            if (f1 < a) dis += a - f1, f1 = a;
            else if (f1 > b) dis += f1 - b, f1 = b;
            if (f2 < a) dis += a - f2, f2 = a;
            else if (f2 > b) dis += f2 - b, f2 = b;
            dis += (f2 - f1) + (t2 - t1);
        }
        printf("%lld\n", dis);
    }
    return 0;
}
```


### B. Badge
-----

题意略。

无脑 n^2 模拟（？？）

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

int n, p[1005], mark[1005];

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d", &p[i]);
    for (int i = 1; i <= n; i++) {
        memset(mark, 0, sizeof(mark));
        int start = i;
        mark[i] = 1;
        while (!mark[p[start]]) {
            start = p[start], mark[start] = 1;
        }
        start = p[start];
        printf("%d ", start);
    }
    printf("\n");
    return 0;
}
```


### C. Elections
-----

题意略。

此题卡了好久！后来干脆弃疗。。。

我一直在想是不是要设什么性价比，或者应该挑当前票最多的对手，拿ta一张票相等于两张之类，但由于很容易证明是错的，也就gg了。。。

正解在此：必定存在一个 x，使得 1 的票数 >= x, 2 ～ m 的票数 < x. 1 <= x <= 3000，枚举就行。n^2。

使所有 2 ～ m 的数量都降至 x 以下，最后若 1 的票数还是不 >= x，就挑剩下来最小的补上。

code:
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

typedef long long ll;
const ll inf = 1e16;
int n, m, c[3010], b[3010];
ll ans, sum;
struct node {int p; ll c;}a[3010];

bool cmp(node a, node b) {return a.c > b.c;}

int main() {
    scanf("%d%d", &n, &m);
    rep(i, 1, n) scanf("%d%lld", &a[i].p, &a[i].c);
    sort(a + 1, a + n + 1, cmp);
    ans = inf;
    rep(i, 1, n) {
        rep(j, 1, m) c[j] = 0;
        sum = 0;
        rep(j, 1, n) b[j] = 0;
        rep(j, 1, n) {
            if (a[j].p == 1) c[1]++;
            else {
                if (c[a[j].p] + 1 >= i) sum += a[j].c, c[1]++, b[j] = 1;
                else c[a[j].p]++;
            }
        }
        for (int j = n; j >= 1; j--)
            if (a[j].p != 1 && b[j] == 0 && c[1] < i) b[j] = 1, sum += a[j].c, c[1]++;
        if (c[1] >= i) ans = min(ans, sum);
    }
    printf("%lld\n", ans);
    return 0;
}
```

### D. The hat
-----

是个交互题？？坑先留着