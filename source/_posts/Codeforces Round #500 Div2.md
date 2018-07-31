---
title: 'Codeforces Round #500 Div2'
date: 2018-07-31 22:36:40
tags: 
    - 比赛
mathjax: true
---

http://codeforces.com/contest/1013

### A. Piles With Stones
-----

题意：有 n 堆石子，每次你可以移走任意一堆里的一颗石子或将一堆里的部分石子移到另一堆。给出操作前的石子堆和操作后的石子堆，判断操作是否合理。

因为操作不能增加石子的总数，所以如果操作后的石子数大于操作前的就输出"No"，否则输出"Yes"。

### B. And
-----

题意：有 n 个数，x，每个数可以 xor x，求至少需要多少次异或，才能有至少两个数相等。

只存在 4 种情况：-1，0，1，2. 对于每个数 ai，如果异或后等于异或前，那么 C[ai]++ ; 否则 C[ai]++, D[ai ^ x]++。

从 0 到 100000 枚举 i，如果 C[i] >= 2, ans 最小，是 0；如果 C[i] = 1, D[i] > 0, ans 可能是 1；如果 C[i] = 0, D[i] >= 2, ans 可能是 2.

### C. Photo of The Sky
-----

题意：有 n 个坐标系上的点，这些点都在一个矩阵里。小 A 记录了它们的 x 坐标和 y 坐标，但顺序打乱了，现在要求你根据这些记录确定矩阵可能的最小面积。

有 2n 个数据，其中必定有 n 个 x 坐标，n 个 y 坐标。先排序。两种坐标必定各自在给出数据的一个区间内，设 x 坐标的区间为 [l, r], y 坐标的区间为 [p, q]。

若 l > 1, r < 2n, 那么 y 坐标的距离就是 a[2n] - a[1]，x 坐标的距离就是 a[r] - a[l]。因为 y 坐标的距离已经确定了，l 和 r 必然是越近越好，即 r = l + n - 1.

若 l = 1, 肯定是 r = l + n - 1, p = r + 1, q = 2n 最优。

比赛时还剩40分钟的样子，还是想出了关于区间的道理的，但打挂了：“l 和 r 必然是越近越好”的最优性没有考虑，导致了一系列奇奇怪怪无法分析的问题。。。

code：
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 1e6 + 10;
typedef long long ll;
ll a[N];

int main() {
    int n;
    ios::sync_with_stdio(false);
    cin >> n;
    for (int i = 1; i <= n * 2; i++) cin >> a[i];
    sort(a + 1, a + 2 * n + 1);
    ll ans = (a[n] - a[1]) * (a[2 * n] - a[n + 1]);
    for (int i = 2; i <= n; i++) ans = min(ans, (a[i + n - 1] - a[i]) * (a[2 * n] - a[1]));
    cout << ans << endl;
    return 0;
}
```

### D. Chemical table
-----

题意：给出一个 $n * m$ 的表格，表格上有一些初始点。若有这样的三个点：(r1, c1), (r1, c2), (r2, c1)，则由这三个点能生成出点 (r2, c2)。现问在初始点的基础上最少添加多少个点，能使得初始点和添加的点及它们生成出来的点能填满整个表格。

可以将每一个点看成是将所在的行和列联系起来。如此一来，对于 (r1, c1) + (r1, c2) + (r2, c1) ⇒ (r2, c2)，我们可以理解为：因为 r1 和 c1，r1 和 c2，r2 和 c1 都相应地联系起来了，那么 r2 和 c2 也联系起来了。所以我们只要把所有的行和列都联系起来，生成出来的点就能填满整个表格。那么问题就转换成问最少建立多少联结，能将所有的行和列都联系起来。可以用并查集解决。

code：
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 2e5 + 5;
int fa[N << 1];

int getfa(int x) {return fa[x] == x ? x : fa[x] = getfa(fa[x]);}

int main() {
    ios::sync_with_stdio(false);
    int n, m, q;
    cin >> n >> m >> q;
    for (int i = 1; i <= n + m; i++) fa[i] = i;
    for (int i = 1; i <= q; i++) {
        int r, c;
        cin >> r >> c;
        int fr = getfa(r), fc = getfa(c + n);
        if (fr != fc) fa[fr] = fc;
    }
    int ans = 0;
    int root = getfa(1);
    for (int i = 1; i <= n + m; i++) {
        int tmp = getfa(i);
        if (tmp != root) ans++, fa[tmp] = root;
    }
    cout << ans << endl;
    return 0;
}
```