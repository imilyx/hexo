---
title: 'NOIP 2018 Training 2'
date: 2018-07-02 13:50:40
tags: 
    - 比赛
mathjax: true
---

### A. ksum
-----------

题意：有一个大小为 n 的正整数数组，算出这个数组的所有字段和，并将这 n(n + 1) / 2 个数降序排列，输出前 k 个数。

优先队列存储子段和，因为是正整数，所以用缩减的方法，每次取队首，前缀和算出 (L + 1, R) 和 (L, R - 1)，再次入队。

CODE：
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 100005;
typedef long long ll;
ll n, k, tot, a[N];
ll sum[N], ans[N];

struct node {
    ll l, r;
    ll val;
    bool operator < (const node & x) const {return val < x.val;}
};

map<ll, ll> mp;
priority_queue<node> pq;

void push_heap(node x) {
    ll num = 1ll * x.l + 1ll * x.r * 1000000;
    if (!mp.count(num)) {
        mp[num] = 1;
        pq.push(x);
    }
}

bool cmp(ll a, ll b) {return a > b;}

bool in(ll x, ll y) {
    return (1 <= x && x <= n && 1 <= y && y <= n);
}

int main() {
    // freopen("ksum.in", "r", stdin);
    // freopen("ksum.out", "w", stdout);

    memset(ans, 0, sizeof(ans));
    scanf("%d%d", &n, &k);
    for (ll i = 1; i <= n; i++) {
        scanf("%d", &a[i]);
        sum[i] = sum[i - 1] + a[i];
    }
    node x;
    x.l = 1, x.r = n, x.val = sum[n];
    push_heap(x);
    while (1) {
        node u = pq.top();
        pq.pop();
        ans[++tot] = u.val;
        if (tot == k) break;
        ll l = u.l, r = u.r;
        node x, y;
        x.l = l + 1, x.r = r, x.val = sum[x.r] - sum[x.l - 1];
        y.l = l, y.r = r - 1, y.val = sum[y.r] - sum[y.l - 1];
        if (l + 1 <= r && in(l + 1, r)) push_heap(x);
        if (l <= r - 1 && in(l, r - 1)) push_heap(y);
    }
    sort(ans + 1, ans + tot + 1, cmp);
    for (ll i = 1; i <= k; i++) printf("%d ", ans[i]);
    putchar('\n');
    return 0;
}
```

### B. label
------------

题意：有一棵大小为 n 的树，节点编号为 1 ～ n，我们给树上的每一个节点赋一个 [1, m] 之间的权值，并使得有边直接相连的两个节点的权值之差的绝对值 >= K。求有多少种不同的赋值方法，答案对 $10^9 + 7$ 取模。

先考虑第一档部分分，很明显的树形DP，$F_{i, j}$ 表示第 i 个节点赋值为 j 的方案数。可得：

$$F_{i, j} = \prod (\sum F_{Son(i), k})$$

其中 $|j - k| \geq K$

第二档也很好办，前缀和优化，每次减去 (j - K) ~ (j + K) 的不符合区间。

然而 $m \leq 10^9$ 实在太大了。
我们发现，对于叶节点，$F_{i, j}$ 均为 1；对于叶节点的父亲节点，F 的值在下标为 [K, m - K] 范围内相同，而在两边对称。进而发现，每一个节点的 F 值都是这样分布的。两边对称值的个数与它到距离最远的后代叶子节点有关。假设这个距离是 x ，那么对称值的个数不超过 xK。

于是我们得到了一个优化的方法，j 的范围只要取到 $min(m, (n - 1) * k)$

$O(n^2*k)$ 

CODE：
``` c++
#include <bits/stdc++.h>
using namespace std;

const int mo = 1e9 + 7;
typedef long long ll;
int T, n, m, k, x, y, l, ans, f[105][10005], u[105];
vector<int> nxt[105];

int quick_pow(int b, int p) {
    int res = 1;
    for (; p; p >>= 1) {
        if (p & 1) res = res * b % mo;
        b = b * b % mo;
    } return res;
}

int calc(int x, int y) {  // 计算f[x][y],f[x][y + 1]...f[x][m]
    int sum = 0;
    for (int i = y; i <= l; i++) sum = (sum + f[x][i]) % mo;
    for (int i = m; i >= m - l + 1; i--) {
        if (i <= l || i < y) break;
        else
            sum = (sum + f[x][m - i + 1]) % mo;  // 对称计算
    }
    int L = max(y, l + 1), R = m - l;
    if (R >= L) sum = (sum + 1ll * (R - L + 1) * f[x][l]) % mo;  // 中间部分
    return sum;
}

void dfs(int x, int fa) {
    for (int i = 1; i <= l; i++) f[x][i] = 1;
    for (int i = 0; i < (int)nxt[x].size(); i++) {
        if (nxt[x][i] == fa) continue;
        dfs(nxt[x][i], x);
        y = calc(nxt[x][i], k + 1);
        for (int j = 1; j <= l; j++) {
            if (j > k) y += f[nxt[x][i]][j - k], y %= mo;
            f[x][j] = 1ll * f[x][j] * y % mo;
            if (j + k <= m) {
                if (j + k <= l) y -= f[nxt[x][i]][j + k];
                else if (j + k > m - l) y -= f[nxt[x][i]][m - j - k + 1];
                else
                    y -= f[nxt[x][i]][l];
            }
        }
    }
}

int main() {
    // freopen("label.in", "r", stdin);
    // freopen("label.out", "w", stdout);

    ios::sync_with_stdio(false);
    cin >> T;
    while (T--) {
        memset(nxt, 0, sizeof(nxt));
        cin >> n >> m >> k;
        for (int i = 1; i < n; i++) {
            cin >> x >> y;
            nxt[x].push_back(y), nxt[y].push_back(x);
        }
        l = min(m, (n - 1) * k);
        if (k < 0) ans = 0;
        else if (!k) ans = quick_pow(m, n);
        else {
            dfs(1, 0);  //  将1作为根
            ans = calc(1, 1);
        }
        cout << ans << endl;
    }
    return 0;
}
```


### C. square
-------------

题意：有一个大小为 $n * m$ 的方格图，有的方格为 1，有的方格为 0。T次询问一个区间，区间左上角(x1, y1) ，右下角(x2, y2) 。求给出区间中最大的全为 1 的正方形的边长。

分情况讨论，1.正方形四边不靠给定区间 2.正方形两边靠给定区间。用DP，或者二分判定边长，好像是这样。。

没有写，有想法再回来。