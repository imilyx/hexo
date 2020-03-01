---
title: 'Codeforces Round 618 Div1'
date: 2020-02-29 13:55:40
tags: 
    - 比赛
mathjax: true
---

第一次 vp Div1 ~

### A. Anu Has a Function
-----

考虑 (x | y) - y 的意义：等同于保留所有**只有 x 为 1**的二进制位

那么就很好办：选择不和其他数撞位的、有最高位的数放第一个

* 二进制题要考虑意义

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

typedef long long ll;
const int N = 1e5 + 10;
ll n, a[N], id[40];

int main() {
    cin >> n;
    rep(i, 1, n) scanf("%lld", &a[i]);
    rep(i, 1, n) {
        ll x = a[i];
        rep(j, 0, 32)
            if ((x >> j) & 1) {
                if (id[j] > 0) id[j] = -1;
                else if (!id[j]) id[j] = i;
            }
    }
    for (int i = 32; i >= 0; i--)
        if (id[i] > 0) { swap(a[1], a[id[i]]); break; }
    rep(i, 1, n) printf("%lld ", a[i]); puts("");
    return 0;
}
```

### C. Water Balance
-----

有点意思。最终，若将相邻、值相等的位置归为一块，必不存在第 i 块的值大于第 i + 1 块的情况。第 i 块如果很小，它肯定会优先与 i - 1 合并而不是和 i + 1（字典序嘛。。）单调栈维护！！

还有一种思路：维护下凸壳，也一样可以做

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 1e6 + 10;
int n;
double a[N], s[N];
struct node { int l, r; double ave; }stk[N];

int main() {
    cin >> n;
    rep(i, 1, n) scanf("%lf", &a[i]), s[i] = s[i - 1] + a[i];

    int top = 0;
    rep(i, 1, n) {
        stk[++top] = (node){i, i, a[i]};
        while (top > 1 && stk[top].ave < stk[top - 1].ave) {
            top--, stk[top].r = stk[top + 1].r;
            stk[top].ave = (s[stk[top].r] - s[stk[top].l - 1]) / (stk[top].r - stk[top].l + 1);
        }
    }
    rep(i, 1, top) rep(j, stk[i].l, stk[i].r) printf("%.9lf\n", stk[i].ave);
    return 0;
}
```