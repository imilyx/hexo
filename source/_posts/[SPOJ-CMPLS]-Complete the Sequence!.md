---
title: '[SPOJ-CMPLS]-Complete the Sequence!'
date: 2018-08-08 21:50:40
tags: 
    - 差分
mathjax: true
---

https://vjudge.net/problem/SPOJ-CMPLS

题意：给出了一个数列的前 s 项，要求推出后面的 c 项。

很像找规律，不要被那个 P(x) 的式子唬住了哈。。。

这个办法就是，差分求多项式值。

给定 f(x) 是关于 x 的一个多项式，设 f(x) 的次数是 n。

则规定:

$a_x = f(x) - f(x - 1)$, 称为一阶差分.

$b_x = a_x - a_{x - 1}$, 称为二阶差分.

$c_x = b_x - b_{x - 1}$, 称为三阶差分.

以此类推，直至 n 阶差分。

根据以上定义，给定 n + 1 组初始的 x 和 f(x) 的映射，即可通过差分法得到往后的所有多项式值。

例：$f(x) = x^3 + x^2 + x + 1$.

``` c++
x  f(x)   ax   bx   cx
0    1
1    4    3
2    15   11   8
3    40   25   14   6
4    85   45   20   6
5   156   71   26   5   <- f(x) = f(x - 1) + a_{x - 1} + b_{x - 1} + c_{x - 1}
```

可以发现，这个三次的多项式在三阶差分时相等。

如上所述，这是个模板题。

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 110;
int T, S, C, X[N], f[N][N];

int main() {
    scanf("%d", &T);
    while (T--) {
        scanf("%d%d", &S, &C);
        for (int i = 0; i < S; i++) scanf("%d", &f[0][i]);
        for (int i = 1; i < S; i++)
            for (int j = 0; j < S - i; j++)
                f[i][j] = f[i - 1][j + 1] - f[i - 1][j];
        for (int i = 1; i <= C; i++) f[S - 1][i] = f[S - 1][i - 1];
        for (int i = S - 2; i >= 0; i--)
            for (int j = S - i; j < S - i + C; j++)
                f[i][j] = f[i + 1][j - 1] + f[i][j - 1];
        for (int i = S; i <= S + C - 1; i++) printf("%d ", f[0][i]);
        printf("\n");
    }
    return 0;
}
```