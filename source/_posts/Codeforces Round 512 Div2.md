---
title: 'Codeforces Round 512 Div2'
date: 2018-10-01 15:36:40
tags: 
    - 比赛
mathjax: true
---

[比赛链接](http://codeforces.com/contest/1058)

题目比较简单，做了四道喔哈哈哈～

### A. In Search of an Easy Problem
-----

$H_2O$ 签到题

### B. Vasya and Cornfield
-----

不知道有没有优美的方法～不过我的比较容易想到，就是把 {(0, d), (d, 0), (n, n - d), (n - d, n)} 区域染色，然后 O(1) 回答询问～

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

int mark[110][110];

int main() {
    int n, d, m, x, y;
    scanf("%d%d%d", &n, &d, &m);
    int dx, dy;
    dx = dy = 1;
    x = -1, y = d + 1;
    for (int i = 0; i <= d; i++) {
        x++, y--;
        for (int j = 0; j <= n - d; j++) mark[x + j][y + j] = 1;
        if (x != d && y != 0)
            for (int j = 0; j < n - d; j++) mark[x + j + 1][y + j] = 1;
    }
    while (m--) {
        scanf("%d%d", &x, &y);
        if (mark[x][y]) puts("YES");
        else puts("NO");
    }
    return 0;
}
```

### C. Vasya and Golden Ticket
-----

因为数据很小，$\sum_{i = 1}^n a_i$ 最多是 900，所以可以从 0 到 tot 枚举单块的数值和，chk函数判断。虽然不能用二分，但暴力枚举也可以过！

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

int n, a[110], tot;
char s[110];

bool chk(int limit) {
    int sum = 0, cnt = 1;
    for (int i = 1; i <= n; i++) {
        sum += a[i];
        if (sum == limit) sum -= limit, ++cnt;
        if (sum > limit) return false;
    }
    if (sum) return false;
    return true;
}

int main() {
    scanf("%d", &n);
    scanf("%s", s + 1);
    for (int i = 1; i <= n; i++) {
        a[i] = s[i] - '0';
        tot += a[i];
    }
    if (!tot) {
        puts("YES"); return 0;
    }
    for (int i = 0; i < tot; i++) {
        if (i > 0 && tot % i > 0) continue;
        if (chk(i)) {
            puts("YES"); return 0;
        }
    }
    puts("NO");
    return 0;
}
```

### D. Vasya and Triangle
-----

因为存在多个答案，是构造题，所以挑最容易构的直角三角形就可以过。

首先--三角形向量面积公司--叉积公式了解一下：$S = \frac{1}{2} |x_1y_2 - x_2y_1|$, 其中 $(x_1, y_1)$ $(x_2, y_2)$ 分别是三角形两边所表示向量的坐标。

首先，根据叉积公式算出来的 2S 一定是整数，那么 $\frac{nm}{k}$ 一定是 0.5 的倍数，即 $\frac{2nm}{k}$ 应该是整数，反之若 $2 * n * m\ mod\ k \neq 0$, 无解.

接下来是有解的情况。

构造直角三角形，相当于构造一个 (0, 0) (X, 0) (Y, 0) 形式的直角三角形，那么 XY = 2S = 2nm / k = nm / (k / 2). 令 d = gcd(n, k), x1 = n / d, y1 = 0, x2 = 0, y2  = m / (k / d), x3 = y3 = 0.

但是因为 k % 2 不一定 = 0, 所以如果 k % 2 = 0, k /= 2; 否则，如果 x1 <= n / 2, x1 = 2x1, 否则 y2 = 2y2.

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
ll n, m, k;

ll gcd(ll a, ll b) { return !b ? a : gcd(b, a % b); }

int main() {
    scanf("%lld%lld%lld", &n, &m, &k);
    if (2 * n * m % k != 0) { puts("NO"); return 0; }
    puts("YES");
    bool flag = false;
    if (k % 2 == 0) k /= 2, flag = true;
    ll d = gcd(n, k), x, y, x2, y2, x3, y3;
    x = n / d, y = 0, x2 = 0, y2 = m / (k / d), x3 = y3 = 0;
    if (!flag) {
        if (x <= n / 2) x *= 2;
        else y2 *= 2;
    }
    printf("%lld %lld\n%lld %lld\n%lld %lld\n", x, y, x2, y2, x3, y3);
    return 0;
}
```