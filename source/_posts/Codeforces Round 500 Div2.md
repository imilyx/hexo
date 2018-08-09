---
title: 'Codeforces Round 500 Div2'
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

### E. Hills
-----

题意：有一座城市，城市中有 n 座山，是从左到右按一行排列的，每座山有一个已知的高度 h，一座房子能在第 i 座山上建造，当且仅当 $h_{i - 1} < h_i$ 且 $h_i > h_{i + 1}$，现在这座城市的市长想对这些山进行修整，他们可以花一天的时间将某一座山的高度减掉1。现在，对于所有的k($1 \leq k \leq \lceil \frac{n}{2} \rceil$)，他们想知道最少需要多少天可以建造出 k 幢房子（除了调整山的高度外，其他时间一律不计）。

DP，设 $f[i][j][k]$ 表示现在 DP 到第 i 座山，前 i 座山里有 j 座可以建房子，状态为 k。

k = 0，表示第 i 座山和第 i - 1 座山都不建房子；

k = 1，表示第 i 座不建，第 i - 1 建；

k = 2，表示第 i 座建，第 i - 1 不建或第 i 座建。

不过为了方便编写，当我们在第 i 座山建房子时，对第 i + 1 座山的影响放在第 i + 1 位进行计算。

$f_{i, j, 0} = min(f_{i - 1, j, 0}, f_{i - 1, j, 1})$ 如果这一座山和前一座山都不是山峰的转移。 

$f_{i, j, 1} = f_{i - 1, j, 2} + max(0, h_i - (h_{i - 1} - 1))$ 这里是计算第 i − 1 座建房子是对第 i 座山的影响。 

$f_{i, j, 2} = min(f_{i - 1, j - 1, 0} + max(0, h_{i - 1} - (h_i - 1)), f_{i - 1, j - 1, 1} + max(0, min(h_{i - 2} - 1, h_{i - 1}) - (h_i - 1)))$ 这里是分两种情况考虑，当第 i − 2 座山也被拿来建房子时，要考虑第 i − 2 座山对第 i − 1 座山的影响与第 i 座山拿来建房子是对第 i − 1 座山的影响。如果第 i − 2 座山不拿来建房子，那就不用考虑了。

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 5005;
int n, h[N], f[N][N][5];

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d", &h[i]);
    memset(f, 0x3f, sizeof(f));
    f[0][0][0] = 0;
    for (int i = 1; i <= n; i++)
        for (int j = 0; j <= i; j++) {
            f[i][j][0] = min(f[i - 1][j][0], f[i - 1][j][1]);
            f[i][j][1] = f[i - 1][j][2] + max(0, h[i] - (h[i - 1] - 1));
            if (j)
                f[i][j][2] = min(f[i - 1][j - 1][0] + max(0, h[i - 1] - (h[i] - 1)), f[i - 1][j - 1][1] + max(0, min(h[i - 2] - 1, h[i - 1]) - (h[i] - 1)));
        }
    for (int i = 1; i <= (n + 1) / 2; i++)
        printf("%d ", min(f[n][i][0], min(f[n][i][1], f[n][i][2])));
    putchar('\n');
    return 0;
}
```