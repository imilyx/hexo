---
title: '[POJ3150]-Cellular Automaton'
date: 2018-06-30 14:50:40
tags: 
    - 矩阵乘法
mathjax: true
---

https://vjudge.net/problem/POJ-3150

题意：有 n 个数围成一个环，现在有一种变换，将所有距离第 i (1 <= i <= n) 个数小于等于 d 的数加起来，对 m 取余。现在要求将所有的数都变换 k 次，得到的 n 个数的值。

根据样例，可以发现可以用以下的矩阵来表示：

``` c++
a =
1 1 0 0 1
1 1 1 0 0
0 1 1 1 0
0 0 1 1 1
1 0 0 1 1

b = 1 2 2 1 2

a * b = 5 5 5 5 4
```

也就是一个 $1 * N$ 的矩阵乘以一个 $N * N$ 的矩阵，仍得到一个 $1 * N$ 的矩阵。

结果就是，$a ^ k * b$ ，复杂度是 $O(n^3logk)$ ，过不了。

然后我们再仔细观察发现 a 这个矩阵很奇特，每一行都是上一行后移一位得到的。
所以我们可以把每个矩阵变成一行，将原来的 a[i][k] 变成 a[i - k] ，最后就是 $O(n^2logk)$ 。

CODE：
``` c++
#include <cstdio>
#include <cstring>
#include <algorithm>
using namespace std;

typedef long long ll;
const int N = 505;
ll n, m, d, k, num[N], a[N], b[N];

void Multi(ll a[], ll b[], ll ans[]) {
    ll t[505];
    memset(t, 0, sizeof(t));
    for (ll i = 0; i < n; i++)
        for (ll k = 0; k < n; k++)
            if (i - k >= 0) t[i] = (t[i] + (a[k] * b[i - k]) % m) % m;
            else t[i] = (t[i] + (a[k] * b[i - k + n]) % m) % m;
    for (ll i = 0; i < n; i++) ans[i] = t[i];
}

int main() {
    scanf("%lld%lld%lld%lld", &n, &m, &d, &k);
    for (ll i = 0; i < n; i++) scanf("%lld", &num[i]);
    for (ll i = 0; i <= d; i++) a[i] = 1;
    for (ll i = n - 1; i >= n - d; i--) a[i] = 1;
    b[0] = 1;
    while (k) {
        if (k & 1) Multi(b, a, b);
        k >>= 1;
        Multi(a, a, a);
    }
    Multi(num, b, num);
    for (ll i = 0; i < n; i++) printf("%lld ", num[i]);
    printf("\n");
    return 0;
}
```