---
title: '[POJ1733]-Parity game'
date: 2018-07-17 15:50:40
tags: 
    - 并查集
mathjax: true
---

https://vjudge.net/problem/POJ-1733

题意：有一个由 0 和 1 组成的序列 S，长度为 N。有 M 句话，每句话回答区间 S[l～r] 中有奇数个 1 还是偶数个 1。但有的话是假话，与之前矛盾的就叫假话。求一个最小的 k，使得存在一个 01 序列满足 1 ～ k - 1 句话，但不存在满足第 1 ～ k 句话的 01 序列。 $N \leq 10^9$ ，M <= 10000。

如果我们用 sum 数组表示序列 S 的前缀和， 那么在每个回答中：
1. S[l ~ r] 有偶数个 1，等价于 sum[l - 1] 与 sum[r] 奇偶性相同。
2. S[l ~ r] 有奇数个 1，等价于 sum[l - 1] 与 sum[r] 奇偶性不同。

好了，又是“相同”和“不同”，可以联系到“程序自动分析”那题，都是给定若干个变量和关系。

序列长度 N 很大，但问题数 M 较小，我们可以将每句话的两个整数 l - 1 和 r 离散化，缩小到等价的 1 ～ 2M 以内的范围。

解决本题有两种很好的方法，第一种解决方法是使用“边带权”的并查集。
边权 d[x] 为 0，表示 x 与 fa[x] 奇偶性相同；为 1，表示 x 与 fa[x] 奇偶性不同。在路径压缩时，对 x 到树根路径上的所有边权做异或操作，即可求出 x 与树根的奇偶性关系。

对于每句话，设在离散化后 l - 1 和 r 的值分别是 x 和 y，设 ans 表示这句话的奇偶性。

先检查 x 和 y 是否在同一个集合内（奇偶关系是否已知）、getfa(x)、getfa(y) 都执行完成后，d[x] xor d[y] 即为 x 和 y 的奇偶性关系。若 d[x] xor d[y] 不等于 ans，则在该问题之后即可确定是假话。

若 x 和 y 不在同一个集合内，那么我们要合并。设两个集合的树根为 p 和 q，要令 p 为 q 的子节点。已知 d[x] 与 d[y] 分别表示路径 x ~ p 与 y ~ q 之间所有边权的 xor 和，p ~ q 之间的 d[p] 是待求的值，那么我们就用 ans 去异或 d[x] xor d[y]。

CODE：
``` c++
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
using namespace std;

const int N = 10010;
struct node {
    int l, r, ans;
} query[N];
int a[N << 1], fa[N << 1], d[N << 1], n, m, t;

int getfa(int w) {
    if (w == fa[w]) return w;
    int root = getfa(fa[w]);
    d[w] ^= d[fa[w]];
    return fa[w] = root;
}

void read_discrete() {
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= m; i++) {
        char str[5];
        scanf("%d%d%s", &query[i].l, &query[i].r, str);
        query[i].ans = (str[0] == 'o' ? 1 : 0);
        a[++t] = query[i].l - 1;
        a[++t] = query[i].r;
    }
    sort(a + 1, a + t + 1);
    n = unique(a + 1, a + t + 1) - a - 1;
}

int main() {
    read_discrete();
    for (int i = 1; i <= n; i++) fa[i] = i;
    for (int i = 1; i <= m; i++) {
        int x = lower_bound(a + 1, a + n + 1, query[i].l - 1) - a;
        int y = lower_bound(a + 1, a + n + 1, query[i].r) - a;
        int fx = getfa(x), fy = getfa(y);
        if (fx == fy) {
            if ((d[x] ^ d[y]) != query[i].ans) {
                printf("%d\n", i - 1);
                return 0;
            }
        } else {
            fa[fx] = fy, d[fx] = d[x] ^ d[y] ^ query[i].ans;
        }
    }
    printf("%d\n", m);
    return 0;
}
```

第二种方法更直观，是“扩展域”的并查集。

把每个变量的信息拆成两个节点 $x_{odd}$ 和 $x_{even}$ ，其中 $x_{odd}$ 表示 sum[x] 是奇数，$x_{even}$ 表示 sum[x] 是偶数。它俩也分别称为 x 的奇数域和偶数域。

对于每句话，设在离散化后 l - 1 和 r 的值分别是 x 和 y，设 ans 表示这句话的奇偶性。

若 ans = 0，则合并 $x_{odd}$ 与 $y_{odd}$ 、$x_{even}$ 与 $y_{even}$ ，表示 “x 为偶数” 与 “y 为偶数”、“x 为奇数” 与 “y 为奇数” 可以互相推出。

若 ans = 1，则合并 $x_{odd}$ 与 $y_{even}$ 、$x_{even}$ 与 $y_{odd}$ ，表示 “x 为奇数” 与 “y 为偶数”、“x 为偶数” 与 “y 为奇数” 可以互相推出。

考虑矛盾的话，跟上面一样的咯。

CODE：
``` c++
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
using namespace std;

const int N = 10010;
struct node {
    int l, r, ans;
} query[N];
int a[N << 1], fa[N << 1], d[N << 1], n, m, t;

int getfa(int w) {return fa[w] == w ? w : fa[w] = getfa(fa[w]);}

void read_discrete() {
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= m; i++) {
        char str[5];
        scanf("%d%d%s", &query[i].l, &query[i].r, str);
        query[i].ans = (str[0] == 'o' ? 1 : 0);
        a[++t] = query[i].l - 1;
        a[++t] = query[i].r;
    }
    sort(a + 1, a + t + 1);
    n = unique(a + 1, a + t + 1) - a - 1;
}

int main() {
    read_discrete();
    for (int i = 1; i <= 2 * n; i++) fa[i] = i;
    for (int i = 1; i <= m; i++) {
        int x = lower_bound(a + 1, a + n + 1, query[i].l - 1) - a;
        int y = lower_bound(a + 1, a + n + 1, query[i].r) - a;
        int x_odd = x, x_even = x + n;
        int y_odd = y, y_even = y + n;
        if (query[i].ans == 0) {
            if (getfa(x_odd) == getfa(y_even)) {
                printf("%d\n", i - 1);
                return 0;
            }
            fa[getfa(x_odd)] = getfa(y_odd);
            fa[getfa(x_even)] = getfa(y_even);
        } else {
            if (getfa(x_odd) == getfa(y_odd)) {
                printf("%d\n", i - 1);
                return 0;
            }
            fa[getfa(x_odd)] = getfa(y_even);
            fa[getfa(x_even)] = getfa(y_odd);
        }
    }
    printf("%d\n", m);
    return 0;
}
```