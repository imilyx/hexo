---
title: 位运算(bits)
date: 2019-01-31 19:59:40
tags: 
mathjax: true
---

题意：
------
定义 G(x) 为 x 的二进制表示中 1 的个数。

小C有一个奇妙的方程

$$x_1\ opt_1\ x_2\ opt_2\ x_3\ ...\ x_{n - 1}\ opt_{n - 1}\ x_n\ =\ c$$

其中 $opt_i$ 为 AND, OR, XOR 三种位运算中的一种。我们定义优先级是相同的，即计算顺序为从左到右。

此外，对于 $x_i$, 须满足 $0 \leq x_i < 2^m$ 且 $G(x_i) = a_i$, 保证 $0 \leq c < 2^m$.

求一组 $x_i$ 的取值满足上述所有约束。若不存在，输出 OvO。

分析
-----

A掉了这道巨题，窝很开心啊( ´▽｀)

这道题嘛，首先要熟悉三种位运算的性质啦。

虽然要给出一组 x 的解，但只要知道了 $G(x_i)$, 就能造出 $x_i$. 所以我们先搞出 $G(x_i)$! 选择DP啦～

设 $f[i, j]$ 表示前 i 个 x 位运算后的结果中的为 1 的二进制位为 j 个。为了方便计算答案，设 $g[i, j]$ 存储前 i 个 x 位运算后的结果中的为 1 的二进制位为 j 个时，设前 i - 1 位个 x 位运算后的结果为 t，G(t) = p ，和 t 与 a[i] 共同有 q 位是同为 1 的。我们可以用 pair 型的来存 (p, q).

转移的时候枚举 $x_i$ 和 $x_{i + 1}$ 共有 k 位同为 1 的，那么分开来讨论就好啦，即从 $f[i, j]$ 转移到 $f[i + 1, ?]$.

统计答案。可以通过结果和第一个加数将第二个加数二进制位上的 1 补齐，即根据结果分三种情况讨论。

挺好的一道题呀，不适合普及组的难度qwq

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

typedef pair<int, int> pii;
const int N = 1e5 + 10;
int n, m, c, c_num;
int a[N], ans[N], f[N][60], tmp[N];
pii g[N][60];
char s[5];

int main() {
    scanf("%d%d%d", &n, &m, &c);
    c_num = c;
    for (int i = 1; i < n; i++) {
        scanf("%s", s);
        if (s[0] == 'X') tmp[i] = 0;
        else if (s[0] == 'A') tmp[i] = 1;
        else if (s[0] == 'O') tmp[i] = 2;
    }
    for (int i = 1; i <= n; i++) scanf("%d", &a[i]);
    int cnt = 0;
    while (c_num) {
        if (c_num & 1) cnt++; c_num >>= 1;
    }
    f[1][a[1]] = 1;
    for (int i = 1; i < n; i++) {
        for (int j = 0; j <= m; j++) {
            if (f[i][j]) {
                for (int k = 0; k <= min(j, a[i + 1]); k++) {  // 枚举 j 和 a[i + 1] 共同为 1 的位数（k）
                    if (j + a[i + 1] - k <= m) {
                        if (tmp[i] == 0) {
                            f[i + 1][j + a[i + 1] - 2 * k] = 1;
                            g[i + 1][j + a[i + 1] - 2 * k] = make_pair(j, k);
                        } else if (tmp[i] == 1) {
                            f[i + 1][k] = 1;
                            g[i + 1][k] = make_pair(j, k);
                        } else if (tmp[i] == 2) {
                            f[i + 1][j + a[i + 1] - k] = 1;
                            g[i + 1][j + a[i + 1] - k] = make_pair(j, k);
                        }
                    }
                }
            }
        }
    }
    if (!f[n][cnt]) {
        puts("OvO"); return 0;
    }
    int now = cnt, result = c;
    for (int i = n; i > 1; i--) {
        pii u = g[i][now];
        int numlst = 0, numnow = 0;
        int cntlst = u.first, cntnow = a[i], both = u.second;
        cntlst -= both, cntnow -= both;
        for (int j = 0; j <= m; j++) {
            if (tmp[i - 1] == 0) {
                if (result & (1 << j)) {
                    if (cntlst) --cntlst, numlst |= (1 << j);
                    else if (cntnow) --cntnow, numnow |= (1 << j);
                } else {
                    if (both) --both, numlst |= (1 << j), numnow |= (1 << j);
                }
            } else if (tmp[i - 1] == 1) {
                if (result & (1 << j)) {
                    if (both) --both, numlst |= (1 << j), numnow |= (1 << j);
                } else {
                    if (cntlst) --cntlst, numlst |= (1 << j);
                    else if (cntnow) --cntnow, numnow |= (1 << j);
                }
            } else {
                if (result & (1 << j)) {
                    if (both) --both, numlst |= (1 << j), numnow |= (1 << j);
                    else if (cntlst) --cntlst, numlst |= (1 << j);
                    else if (cntnow) --cntnow, numnow |= (1 << j);
                }
            }
        }
        ans[i] = numnow;
        result = numlst;
        now = u.first;
    }
    ans[1] = result;
    for (int i = 1; i <= n; i++) {
        printf("%d ", ans[i]);
    }
    puts("");
    return 0;
}
```