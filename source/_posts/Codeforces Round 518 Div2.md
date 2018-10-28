---
title: 'Codeforces Round 518 Div2'
date: 2018-10-28 22:30:40
tags: 
    - 比赛
mathjax: true
---

[比赛链接](http://codeforces.com/contest/1068)

是vp的

### A. Birthday
-----

code:
``` c++
#include<bits/stdc++.h>
typedef long long ll;
using namespace std;

ll n, m, k, l;

int main() {
    while(cin >> n >> m >> k >> l) {
        if (k + l > n || m > n) {
            puts("-1"); continue;
        }
        ll tmp;
        if ((l + k) % m == 0) tmp = (l + k) / m;
        else tmp = (l + k) / m + 1;
        if (tmp * m > n) puts("-1");
        else cout << tmp << endl;
    }
    return 0;
}
```

### B. LCM
-----

数学妙题，$\frac{[a, b]}{a}=\frac{b}{gcd(a, b)}$, 所以其实就是求 b 的约数个数。

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 1e5 + 10;
typedef long long ll;
ll vis[N], prime[N], cnt = 0;

void Prime() {  // 筛质数
    memset(vis, false, sizeof(vis));
    for (ll i = 2; i <= 100000; i++) {
        if (!vis[i]) {
            prime[cnt++] = i;
            for (ll j = i * i; j <= 100000; j += i) vis[j] = true;
        }
    }
}

ll work(ll n) { // 计算约数个数
    ll ans, sum;
    ans = 0, sum = 1;
    for (int i = 0; i < cnt && prime[i] * prime[i] <= n; i++) {
        ans = 0;
        while (n % prime[i] == 0) {
            ans++; n /= prime[i];
        }
        sum *= (ans + 1);
    }
    if (n > 1) sum *= (1 + 1);
    return sum;
}

int main() {
    Prime();
    ll n;
    while (cin >> n) {
        cout << work(n) << endl;
    }
    return 0;
}
```

### C. Colored Rooks
-----

构造题，应该要用尽量简单的方式。比如说这题，可以在 (i, i) 上放第 i 种颜色的车，这样保证每种颜色都占据一格且互不相连。接下来可以在互不相连的基础上添加相连颜色的车，即如果颜色 a 和颜色 b 是一对，那么就在 (a, j) 和 (b, j) 上放一对车。

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

int n, m;
vector<pair<int, int> > pos[105];

int main() {
    cin >> n >> m;
    int x, y;
    for (int i = 1; i <= n; i++) pos[i].push_back(make_pair(i, i));
    for (int i = 1; i <= m; i++) {
        cin >> x >> y;
        pos[x].push_back(make_pair(x, n + i));
        pos[y].push_back(make_pair(y, n + i));
    }
    for (int i = 1; i <= n; i++) {
        cout << pos[i].size() << endl;
        for (int j = 0; j < (int)pos[i].size(); j++)
            cout << pos[i][j].first << ' ' << pos[i][j].second << endl;
    }
    return 0;
}
```