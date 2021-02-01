---
title: '[BZOJ1009]-GT考试'
date: 2019-05-05 18:56:40
tags: 
    - 矩阵乘法
    - KMP
mathjax: true 
---

[题目链接](https://www.lydsy.com/JudgeOnline/problem.php?id=1009)

一直在想组合数学，容斥怎么做。。。看了题解发现是矩阵乘法 + 字符串。。。

一般矩阵乘法的题都得先找递推式。这题的转移方程是:

$$f[i, j] = \sum\limits_{k = 0}^{m - 1} f[i - 1, k] * g[k][j]\ (0 \leq j < m)$$

（$0 \leq j < m$ 是因为题目要求准考证号不完全出现 A 的方案数）

其中, $f[i, j]$ 表示准考证号前 $i$ 位中的后 $j$ 位与 $A$ 的前 $j$ 位相同的方案数, $g[k, j]$ 表示准考证号匹配 $A$ 的前 $k$ 位时，准考证号增加一个字符，使 $A$ 沿着失配指针找到的最大匹配位数 $j$ 的方案数（计算 $g$ 时要从 $0$ 到 $9$ 枚举增加的那个字符是啥）。

我们发现，$f[i, ]$ 每次都是由 $f[i - 1, ]$ 转移来的，所以对 $g$ 矩阵做快速幂就行，$O(m^3\ log\ n)$。

最后的答案就是 $\sum\limits_{i = 0}^{m - 1}g[0, i]$ 。为啥呢？

根据矩阵乘法，我们可知：转移 $k$ 次，$g[k, j]$ 就表示一开始准考证号匹配 $A$ 的前 $k$ 位时，准考证号增加 $k$ 个字符，使 $A$ 沿着失配指针找到的最大匹配位数 $j$（算上新匹配的 $k$ 位）的方案数。

同理，转移 $n$ 次，$g[i, j]$ 就表示一开始准考证号匹配 $A$ 的前 $i$ 位时，准考证号增加 $n$ 个字符，使 $A$ 沿着失配指针找到的最大匹配位数 $j$（算上新匹配的 $n$ 位）的方案数。

我们又知道，一开始准考证号匹配 $A$ 的 $0$ 位，所以最后的答案就是 $\sum\limits_{i = 0}^{m - 1}g[0, i]$ 。

有一定的思维难度，重要的还是转移方程式吧。。额，对于我这个 DP 渣渣来说，DP 题还要切很多。。。

code :
``` c++
#include <bits/stdc++.h>
using namespace std;

int n, m, mod;
int nxt[30], b[30][30], a[30][30];
char s[30];

void mul(int a[30][30], int b[30][30], int ans[30][30]) {
    int tmp[30][30];
    for (int i = 0; i < m; i++)
        for (int j = 0; j < m; j++) {
            tmp[i][j] = 0;
            for (int k = 0; k < m; k++)
                tmp[i][j] = (tmp[i][j] + a[i][k] * b[k][j]) % mod;
        }
    for (int i = 0; i < m; i++)
        for (int j = 0; j < m; j++)
            ans[i][j] = tmp[i][j];
}

int main() {
    scanf("%d%d%d", &n, &m, &mod);
    scanf("%s", s + 1);
    nxt[0] = nxt[1] = 0;
    int j = 0;
    for (int i = 2; i <= m; i++) {
        while (j && s[j + 1] != s[i]) j = nxt[j];
        nxt[i] = (s[j + 1] == s[i] ? ++j : 0);
    }
    for (int i = 0; i < m; i++) {
        for (int j = 0; j <= 9; j++) {
            int t = i;
            while (t && s[t + 1] - '0' != j) t = nxt[t];
            if (s[t + 1] - '0' == j) ++t;
            if (t != m)
                b[i][t] = (b[i][t] + 1) % mod;
        }
    }
    for (int i = 0; i < m; i++) a[i][i] = 1;  // 单位矩阵
    for (; n ; n >>= 1) {
        if (n & 1) mul(a, b, a);
        mul(b, b, b);
    }
    int res = 0;
    for (int i = 0; i < m; i++) res = (res + a[0][i]) % mod;
    printf("%d\n", res);
    return 0;
}
```