---
title: 'Codeforces Round 574 Div2'
date: 2019-07-25 18:55:40
tags: 
    - 比赛
mathjax: true
---

### A. Drinks Choosing
-----

就是本着尽量别浪费的原则。。

签到题硬是读了 N 遍题意。。英语不行啊

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 1e3 + 10;
int n, k, num[N];

int main() {
    cin >> n >> k;
    rep(i, 1, n) {
        int x; cin >> x;
        num[x]++;
    }
    int tot = (n + 1) / 2, ans = 0;
    rep(i, 1, k) {
        ans += num[i] / 2 * 2;
        tot -= num[i] / 2;
    }
    ans += tot;
    cout << ans;
    return 0;
}
```

### B. Sport Mafia
-----

解了个二元一次方程（噗

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

typedef long long ll;
ll n, k;

int main() {
    cin >> n >> k;
    ll a = 1, b = -(n + 1 + n) - 2, c = (n + 1) * n - 2 * k;
    ll tmp = b * b - 4 * a * c;
    tmp = (ll)sqrt(tmp);
    ll ans1 = (-b + tmp) / (2 * a), ans2 = (-b - tmp) / (2 * a);
    if (ans2 >= 0) printf("%lld\n", ans2);
    else printf("%lld\n", ans1);
    return 0;
}
```

### C. Basketball Exercise
-----

DP? 很简单

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 1e5 + 10;
typedef long long ll;
ll n, a[N], b[N], dp[N][3];

int main() {
    cin >> n;
    rep(i, 1, n) {
        scanf("%lld", &a[i]);
    }
    rep(i, 1, n) {
        scanf("%lld", &b[i]);
    }
    rep(i, 1, n) {
        dp[i][0] = max(dp[i - 1][1], dp[i - 1][2]) + a[i];
        dp[i][1] = max(dp[i - 1][0], dp[i - 1][2]) + b[i];
        dp[i][2] = max(dp[i - 1][0], max(dp[i - 1][1], dp[i - 1][2]));
    }
    printf("%lld\n", max(dp[n][0], max(dp[n][1], dp[n][2])));
    return 0;
}
```

### D1. Submarine in the Rybinsk Sea (easy edition)
-----

考虑按位处理

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int mod = 998244353, N = 1e5 + 10;
typedef long long ll;
int n, a[N], num[30];

int main() {
    cin >> n;
    rep(i, 1, n) {
        scanf("%lld", &a[i]);
        ll x = a[i];
        int pos = 1;
        while (x) {
            num[pos * 2 - 1] = (num[pos * 2 - 1] + (x % 10) * n) % mod;
            num[pos * 2] = (num[pos * 2] + (x % 10) * n) % mod;
            x /= 10;
            pos++;
        }
    }
    int len = 0;
    rep(i, 1, 20) {
        if (num[i]) len = i;
        num[i + 1] += num[i] / 10;
        num[i] %= 10;
    }
    while (num[len + 1]) ++len;
    ll ans = 0;
    for (int i = len; i >= 1; i--) {
        ans = (ans * 10 + num[i]) % mod;
    }
    printf("%lld\n", ans);
    return 0;
}
```

### D2. Submarine in the Rybinsk Sea (hard edition)
-----

预处理第 i 个数和长度为 j 的串在一起的贡献，还是按位处理

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int mod = 998244353, N = 1e5 + 10;
typedef long long ll;
int n, a[N], num[30], len[15], pw[35], f[N][12];

int main() {
    cin >> n;

    pw[0] = 1;
    rep(i, 1, 30) pw[i] = pw[i - 1] * 10 % mod;
    rep(i, 1, n) 
        scanf("%lld", &a[i]);
    rep(i, 1, n) {
        ll x = a[i];
        int pos = 0;
        while (x) {
            x /= 10; pos++;
        }
        len[pos]++;
        rep(j, 1, 10) {
            if (pos >= j) {
                ll tmp = a[i];
                rep(k, 0, j - 1) {
                    int x = tmp % 10; tmp /= 10;
                    f[i][j] = (((f[i][j] + 1ll * x * pw[k * 2] % mod) % mod) + 1ll * x * pw[k * 2 + 1] % mod) % mod;
                }
                int p = 2 * j;
                rep(k, j, pos - 1) {
                    int x = tmp % 10; tmp /= 10;
                    f[i][j] = (f[i][j] + 2ll * x * pw[p] % mod) % mod;
                    ++p;
                }
            } else {
                int tmp = a[i];
                rep(k, 0, pos - 1) {
                    int x = tmp % 10; tmp /= 10;
                    f[i][j] = (((f[i][j] + 1ll * x * pw[k * 2] % mod) % mod) + 1ll * x * pw[k * 2 + 1] % mod) % mod;
                }
            }
        }
    }
    ll ans = 0;
    rep(i, 1, n)
        rep(j, 1, 10)
            ans = (ans + 1ll * f[i][j] * len[j] % mod) % mod;
    printf("%lld\n", ans);
    return 0;
}
```

### E. OpenStreetMap
-----

基础套路题！单调队列就可以

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 3e3 + 10;
typedef long long ll;
typedef pair<ll, int> pii;
int n, m, a, b;
ll g, x, y, z, mp[N][N], f[N][N];
deque<pii> q;

int main() {
    cin >> n >> m >> a >> b;
    cin >> g >> x >> y >> z;
    rep(i, 1, n) rep(j, 1, m) {
        mp[i][j] = g; g = (1ll * g * x % z + y) % z;
    }
    rep(i, 1, n) {
        while (!q.empty()) q.pop_back();
        for (int j = m; j >= 1; j--) {
            while (q.size() && mp[i][j] < q.front().first) q.pop_front();
            q.push_front(make_pair(mp[i][j], j));
            f[i][j] = q.back().first;
            while (!q.empty() && q.back().second >= j + b - 1) q.pop_back();
        }
    }
    rep(i, 1, n) {
        rep(j, 1, m) mp[i][j] = f[i][j];
    }

    rep(j, 1, m) {
        while (q.size()) q.pop_back();
        for (int i = n; i >= 1; i--) {
            while (q.size() && mp[i][j] < q.front().first) q.pop_front();
            q.push_front(make_pair(mp[i][j], i));
            f[i][j] = q.back().first;
            while (q.size() && q.back().second >= i + a - 1) q.pop_back();
        }
    }
    ll ans = 0;
    rep(i, 1, n - a + 1) rep(j, 1, m - b + 1) ans += f[i][j];
    printf("%lld\n", ans);
    return 0;
}
```

### F. Geometers Anonymous Club
-----

补了 F！

题意就是让你对一个区间内的凸包进行闵可夫斯基求和。

有一个闵可夫斯基求和的定理，就是 n 个方向不同的向量形成的凸包有 n 个顶点。

离线处理，用树状数组维护。注意，多点一线这种情况要处理！见代码。

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
#define lowbit(x) ((x) & (-(x)))
using namespace std;

const int N = 5e5 + 10, inf = 0x3f3f3f3f;
typedef pair<int, int> pii;
int n, q, l[N], r[N], C[N], ans[N];
map<pii, int> mp;
vector<pii> ve, tmp, query;
vector<int> t[N];

pii get_dir(int x, int y) {  // 处理多点一线的情况
    int gcd = __gcd(abs(x), abs(y));
    if (gcd) x /= gcd, y /= gcd;
    return make_pair(x, y);
}

void add(int x, int val) {
    for (; x <= N; x += lowbit(x)) C[x] += val;
}

int get_sum(int x) {
    int ret = 0;
    for (; x; x -= lowbit(x)) ret += C[x];
    return ret;
}

int main() {
    cin >> n;
    ve.push_back(make_pair(-1, -1));
    rep(i, 1, n) {
        int x; scanf("%d", &x);
        rep(j, 1, x) {
            int a, b; scanf("%d%d", &a, &b);
            tmp.push_back(make_pair(a, b));
        }
        l[i] = ve.size();
        for (int j = 0; j < tmp.size(); j++)
            ve.push_back(get_dir(tmp[j].first - tmp[(j + 1) % tmp.size()].first, tmp[j].second - tmp[(j + 1) % tmp.size()].second));
        tmp.clear();
        r[i] = ve.size() - 1;
    }

    scanf("%d", &q);
    rep(i, 1, q) {
        int a, b; scanf("%d%d", &a, &b);
        pii temp = make_pair(l[a], r[b]);
        query.push_back(temp);
        t[temp.second].push_back(i - 1);
    }
    for (int i = 1; i < ve.size(); i++) {
        if (mp.count(ve[i])) add(mp[ve[i]], -1);
        add(i, 1);
        mp[ve[i]] = i;
        for (int j = 0; j < t[i].size(); j++)
            ans[t[i][j]] = get_sum(query[t[i][j]].second) - get_sum(query[t[i][j]].first - 1);
    }
    rep(i, 0, q - 1) printf("%d\n", ans[i]);
    return 0;
}
```