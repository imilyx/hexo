---
title: 'Codeforces Global Round 1'
date: 2019-02-08 18:59:40
tags: 
    - 比赛
mathjax: true
---

做了 A 和 B，也算不错啦

[比赛链接](http://codeforces.com/contest/1110)

### A. Parity
-----

奇偶性判断，注意 $a_k$ 的情况就好啦。（即 $b^0$ ）.

### B. Tape
-----

老了，脑子不好使啦... 想了半天，最开始想到的是隔板法，放 k - 1 个板，但是后来发现只要稍加转换就能变为，b[n] - b[1] + 1 - sum, 其中 sum 为最大的 k - 1 个间隔长度之和。

code :
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 1e5 + 10;
int n, m, k;
int b[N], c[N];
bool cmp(int a, int b) { return a > b; }

int main() {
    scanf("%d%d%d", &n, &m, &k);
    for (int i = 1; i <= n; i++) scanf("%d", &b[i]);
    int tot = b[n] - b[1] + 1;
    for (int i = 1; i < n; i++) c[i] = b[i + 1] - b[i] - 1;
    sort(c + 1, c + n, cmp);
    for (int i = 1; i < k; i++) tot -= c[i];
    printf("%d\n", tot);
    return 0;
}
```

### C. Meaningless Operations
-----

设 a 的最高位是第 x 位，则 $2^x \leq a$, $a_{max} = 2^x - 1$. 设 $b = 2^x - 1\oplus a$, 则 $f(a) = max\{gcd(2^x - 1, 0)\}$ , 即 $2^x - 1$，为最大值。

若 $a = 2^x - 1$，因为 b < a = $2 ^ x - 1$, 所以 $f(a) = max\{gcd(2^x - 1 - b, b)\}$, 因为 $gcd(x, y) = gcd(x, x + y)$, 所以 $f(a) = max\{gcd(2^x - 1, b)\}$，这时找到 $2^x - 1$ 的最小质因子 t，答案就是 $\frac{2^x - 1}{t}$.

code :
``` c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
const int N = 1e6 + 10, inf = 1e9 + 7, mod = 998244353;
int q, x;

int main() {
    scanf("%d", &q);
    while (q--) {
        scanf("%d", &x);
        int maxn = 1;
        while (maxn <= x) maxn <<= 1;
        maxn--;
        if (x == maxn) {
            int flag = 0;
            for (int i = 2; i * i <= maxn; i++) if (maxn % i == 0) { flag = maxn / i; break; }
            if (!flag) puts("1"); else printf("%d\n", flag);
        } else printf("%d\n", maxn);
    }
    return 0;
}
```

### D. Jongmah
-----

一个挺暴力的题，但官方正解是 DP。

首先！因为 [x, x, x], [x + 1, x + 1, x + 1], [x + 2, x + 2, x + 2] 总能被三组 [x, x + 1, x + 2] 所代替，我们设这时就尽量用 [x, x + 1, x + 2].

这时可以这样：设 dp[i, t1, t2] 表示前 i 种数字，有 t1 组 [i - 1, i, i + 1], t2 组 [i, i + 1, i + 2], 我们枚举 t3 表示有 t3 组 [i + 1, i + 2, i + 3], 然后转移到 dp[i + 1, t2, t3].

code :
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 1e6 + 10;
int n, m, x, a[N], dp[N][3][3];

int main() {
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n; i++) {
        scanf("%d", &x); a[x]++;
    }
    for (int i = 1; i <= m; i++) {
        for (int j = 0; j < 3; j++)
            for (int k = 0; k < 3; k++)
                for (int l = 0; l < 3; l++) {
                    if (j + k + l > a[i]) continue;
                    dp[i][k][l] = max(dp[i][k][l], dp[i - 1][j][k] + l + (a[i] - j - k - l) / 3);
                }
    }
    printf("%d\n", dp[m][0][0]);
    return 0;
}
```

### E. Magic Stones
-----

可以这样看：设 $d_i = c_{i + 1} - c_i$, $c_i^, = c_{i + 1} + c_{i - 1} - c_{i}$.

当将 $c_i$ 变为 $c_i^,$ 时：

· $d_{i - 1} = c_i - c_{i - 1} = c_{i + 1} - c_i = d_i$

· $d_i = c_{i + 1} - c_i = c_i - c_{i - 1} = d_{i - 1}$

发现了吗？$d_i$ 与 $d_{i - 1}$ 交换了！

所以不管序列 c 怎么变，序列 d 总是不变的。所以只要判断两个序列的序列 d 是否相同就可以了。

注意！需要判断 s[1] 是否等于 t[1]。因为第一个位置是变不了的。

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
#define per(i, x, y) for (int i = x; i >= y; i--)
using namespace std;

const int N = 1e5 + 10;
int n, c[N], t[N];

int main() {
    scanf("%d", &n);
    rep(i, 1, n) scanf("%d", &c[i]);
    rep(i, 1, n) scanf("%d", &t[i]);
    if (c[1] != t[1]) { puts("No"); return 0; }
    per(i, n, 1) c[i] -= c[i - 1];
    per(i, n, 1) t[i] -= t[i - 1];
    sort(c + 1, c + n + 1);
    sort(t + 1, t + n + 1);
    bool ok = true;
    rep(i, 1, n) if (c[i] != t[i]) { ok = false; break; }
    if (ok) puts("Yes"); else puts("No");
    return 0;
}
```