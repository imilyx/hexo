---
title: 'Codeforces Round 513 Div2'
date: 2018-10-04 19:36:40
tags: 
    - 比赛
mathjax: true
---

[比赛链接](http://codeforces.com/contest/1060)

这次打了两道，但T2虽然思路没错，但细节处理错误，GG了。。。不过这次题目还是很有趣的！！补了4道！！

### A. Phone Numbers
-----

H2O题。ans = min(n / 11, sum)，其中 sum 是 8 的个数。

### B. Maximum Sum of Digits
-----

暴力找一下规律，发现如果 n 有 x 位，那么 a 就是 x 位的：(n 最高位数字 - 1) + 9 + 9 + 9 ... + 9. 其中有 x - 1 个 9. b = n - a.

不要忘了判断，如果 n 本身就是 k + 9 + 9 ... + 9 形式的，a = k, b = 0. (啊啊啊在这里玩脱了。。。直接输出 n 了。。。应该是 S(n) 啊

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
ll n;

ll get(ll x) {
    ll sum = 1;
    while (x) {
        sum *= 10; x /= 10;
    } return sum / 10; 
}

ll S(ll x) {
    ll sum = 0;
    while (x) {
        sum += x % 10; x /= 10;
    } return sum;
}

int main() {
    scanf("%lld", &n);
    if (n < 10) {
        printf("%lld\n", n); return 0;
    } else {
        ll bound = get(n);
        ll x = n - (n % bound);
        if (n == x + bound - 1) {
            printf("%lld\n", S(n)); return 0;
        }
        ll a = n - (n % bound) - 1, b = n - a;
        printf("%lld\n", S(a) + S(b));
    }
    return 0;
}
```

### C. Maximum Subrectangle
-----

比赛时想到了 $c_{i, j}$ 是个摆设，要拆的，但一直在想怎么枚举 x1, x2, y1, y2, 实际不用这样的。

首先拆公式：

$$\sum\limits_{i = x1}^{x2} \sum\limits_{j = y1}^{y2} c_{i, j} = (\sum\limits_{i = x1}^{x2}a_i) * (\sum\limits_{j = y1}^{y2}b_j)$$

然后我们可以 $O(n^2)$ 预处理出每一段 a 的区间 [l, r]。记 $\sum\limits_{i = l}^r a_i$ 为 S，则我们开一个 $2000 ^ 2$ 的数组 A，A[S] = max(A[S], r - l + 1). 即以区间和为下标，长度为数值。对于 b 数组一样的操作。

然后用 two pointers，i 从 $2000 ^ 2$ 开始往小扫，pos只会越来越大。应该是 O(n)。同时统计答案。

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
const int N = 2010;
int n, m;
int a[N], b[N], A[N * N], B[N * N];
ll x;

int main() {
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n; i++) scanf("%d", &a[i]);
    for (int i = 1; i <= m; i++) scanf("%d", &b[i]);
    for (int i = 1; i <= n; i++) {
        int sum = 0;
        for (int j = i; j <= n; j++)
            sum += a[j], A[sum] = max(A[sum], j - i + 1);
    }
    for (int i = 1; i <= m; i++) {
        int sum = 0;
        for (int j = i; j <= m; j++)
            sum += b[j], B[sum] = max(B[sum], j - i + 1);
    }
    scanf("%lld", &x);
    int MAXSUM = 2000 * 2000, pos = 1, ans = 0, now = 0;
    for (int i = MAXSUM; i >= 1; i--)
        if (A[i]) {
            while (pos <= MAXSUM && 1ll * i * pos <= x) now = max(now, B[pos++]);
            ans = max(ans, A[i] * now);
        }
    printf("%d\n", ans);
    return 0;
}
```

### D. Social Circles
-----

这题超好玩！！

是这样的，每个人的 $l_i$ 必定要和自己或另一个人的 $r_i$ 匹配，贡献是 $max(l_i, r_i)$. 所以我们要最小化 $max(l_i, r_i)$. 这时候就不要将第 i 个人的 l 和 r 绑定了。

分别排序 l 和 r。答案就是 $\sum_{i = 1}^n max(l_i, r_i)$.

我们可以这样考虑：若 $l_n$ 是 l 数组和 r 数组中的最大值，它的匹配值必然是尽量大的、接近它的，即 $r_n$。剩下的 $2 * (n - 1)$ 个元素又是这样一个子问题，用数学归纳法即可证明。

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
const int N = 1e5 + 10;
int n, l[N], r[N];

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d%d", &l[i], &r[i]);
    sort(l + 1, l + n + 1);
    sort(r + 1, r + n + 1);
    ll ans = n;
    for (int i = 1; i <= n; i++) ans += max(l[i], r[i]);
    printf("%lld\n", ans);
    return 0;
}
```

### E. Sergey and Subway
-----

这题的意思很好理解。是一道树形DP，还没有涉及过的。

首先来考虑一个基础问题：给定一棵树，求其中每对节点的距离只和，即 $\sum\limits_{i = 1}^n \sum\limits_{j = 1}^n dist(i, j)$.

我们先定义一些量：sum[x] 表示 x 子树中的点到 x 的距离之和，cnt[x] 表示 x 子树中的点的数量，ans即答案，v即 x 的儿子节点。

伪代码如下：

``` c++
(进入 x 子树)
1. sum[x] = 0, cnt[x] = 1
2. dfs(v)
3. sum[v] += cnt[v]  // v 到 x 之间有一步之差，先统计到 sum[v] 上去
4. ans += sum[x] * cnt[v] + sum[v] * cnt[x]  // sum[x] 此时是 x 一部分儿子的距离之和。这一步可以视为两个集合合并的过程
5. sum[x] += sum[v], cnt[x] += cnt[v]
```

那么 E 题是一道进阶版的。对于点 (u, v), 现在的 dist(u, v) (表示为 Now)等于基础问题中 dist(u, v) (表示为 Bef)除以二上取整。有的 Bef = 2Now，有的 Bef = 2Now - 1.

不可能整体除以二上取整，但正因为是除以二，数据少，可以手动处理。我们新建一些量：sum[x, 0] 表示 x 子树中到 x 的距离为偶数的点到 x 的距离，sum[x, 1] 对应的就是奇数；cnt[x, 0] 表示 x 子树中到 x 的距离为偶数的点数，cnt[x, 1] 对应的就是奇数；ans[0] 表示距离为偶数的点对的距离之和，ans[1] 对应的就是奇数。

由于答案 = (ans[0] / 2) + (ans[1] + 距离为奇数的点对数量) / 2，我们再开一个数组 C[], C[1] 表示距离为奇数的点对数量，方便最后计算。

五步分别对应：
``` c++
1. sum[x][0] = sum[x][1] = cnt[x][1] = 0, cnt[x][0] = 1.
2. dfs(v)
3. sum[v][0] += cnt[v][0], sum[v][1] += cnt[v][1].
   swap(sum[v][0], sum[v][1]), swap(cnt[v][0], cnt[v][1])  // 因为增加了 v 到 x 的一步之差，路径奇偶改变
4. ans[(j + k) & 1] += sum[x][j] * cnt[v][k] + sum[x][k] * cnt[v][j]
   C[(j + k) & 1] += cnt[x][j] * cnt[v][k]
5. sum[x][j] += sum[v][j], cnt[x][j] += cnt[v][j].
```

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
const int N = 2e5 + 10;
struct edge {
    int nxt, to;
}e[N << 1];
int n, lnk[N], tot;
ll sum[N][2], cnt[N][2], ans[2], C[2];

void add(int u, int v) {
    e[++tot].to = v, e[tot].nxt = lnk[u], lnk[u] = tot;
}

void dfs(int x, int fa) {
    cnt[x][0] = 1;
    for (int i = lnk[x]; i; i = e[i].nxt) if (e[i].to != fa) {
        int v = e[i].to;
        dfs(v, x);
        sum[v][0] += cnt[v][0], sum[v][1] += cnt[v][1];
        swap(sum[v][0], sum[v][1]);
        swap(cnt[v][0], cnt[v][1]);
        for (int j = 0; j < 2; j++) {
            for (int k = 0; k < 2; k++) {
                ans[(j + k) & 1] += sum[x][j] * cnt[v][k] + cnt[x][j] * sum[v][k];
                C[(j + k) & 1] += cnt[x][j] * cnt[v][k];
            }
        }
        for (int j = 0; j < 2; j++) sum[x][j] += sum[v][j], cnt[x][j] += cnt[v][j];
    }
}

int main() {
    scanf("%d", &n);
    for (int i = 1; i < n; i++) {
        int u, v;
        scanf("%d%d", &u, &v);
        add(u, v), add(v, u);
    }
    dfs(1, 0);
    printf("%lld\n", (ans[1] + C[1]) / 2 + ans[0] / 2);
    return 0;
}
```