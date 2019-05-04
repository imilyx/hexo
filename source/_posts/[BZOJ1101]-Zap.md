---
title: '[BZOJ1101]-Zap'
date: 2018-08-01 20:56:40
tags: 
    - 莫比乌斯
mathjax: true
---

https://vjudge.net/problem/HYSBZ-1101

题意略。

求 $gcd(x, y) = d$, 相当于求 $gcd(x / d, y / d) = 1$ 的个数。相当于求 $1 \leq x \leq a / d$, $1 \leq y \leq b / d$ 中 x, y 互质对数。

令 $n = \lfloor a / d \rfloor$, $m = \lfloor b / d \rfloor$.

即

$$\sum\limits_{i = 1}^a \sum\limits_{j = 1}^b [gcd(i, j) == d]$$

$$= \sum\limits_{i = 1}^n \sum\limits_{j = 1}^m [gcd(i, j) == 1]$$

现在必须提到一个莫比乌斯函数的性质：
当 n > 1 时，
$\sum\limits_{d | n} \mu(d) = 0$；n = 1 时，$\sum\limits_{d | n} \mu(d) = 1$。

证明：

$n = 1$ 时显然。

$n > 1$ 时，设 $n = p_1^{c_1} * p_2^{c_2} * p_3^{c_3} * ... * p_k^{c_k}$ ，d 有其中一部分。

显然根据莫比乌斯函数的定义，d 的每个质因子指数为 1 才有贡献，否则 $\mu(d) = 0$。

那么设 d 中有 r 个质因子。

$\mu(d) = (-1)^r$ ，这样的 d 有 $C_m^r$ 个。

所以

$$\sum\limits_{d | n} \mu(d) = \sum\limits_{r = 0}^m (-1)^rC_m^r$$

我们根据二项式定理，逆推回去：

$$\sum\limits_{r = 0}^m(-1)^rC_m^r = \sum\limits_{r = 0}^mC_m^r(-1)^r1^{m - r} = (-1 + 1)^m = 0$$

证毕。

我们用这个性质把求和的式子变成：

$$\sum\limits_{i = 1}^n \sum\limits_{j = 1}^m \sum\limits_{d | gcd(i, j)} \mu(d)$$

其中 $d | gcd(i, j)$ 可以变为 $d | i\ \ and\ \ d | j$ ，更换求和指标，

$$= \sum\limits_{d = 1}^n \mu(d) * \lfloor \frac{n}{d} \rfloor * \lfloor \frac{m}{d} \rfloor$$

容易知道 $\lfloor n / d \rfloor$ 单调不上升，根据整数分块的知识，最多有 $2\sqrt{n}$ 种不同的取值。所以按取值分成 $O(\sqrt{n})$ 个段分别处理，一个连续的段内的和可以用预处理出的莫比乌斯函数前缀和求出。

初次涉及，觉得非常妙，自己想是绝不可能想到的，只能看题解，但据说是套路题，多做做，要有信心。

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 5e4 + 10;
int T, a, b, d, tot;
int miu[N], sum[N], pri[N];
bool vis[N];

void get_prefix() {
    miu[1] = 1;
    for (int i = 2; i <= 50000; i++) {
        if (!vis[i]) pri[++tot] = i, miu[i] = -1;
        for (int j = 1; j <= tot && i * pri[j] <= 50000; j++) {
            vis[i * pri[j]] = 1;
            if (i % pri[j] == 0) {
                miu[i * pri[j]] = 0;
                break;
            } else miu[i * pri[j]] = -miu[i];
        }
    }
    for (int i = 1; i <= 50000; i++) sum[i] = sum[i - 1] + miu[i];
}

int cal(int n, int m) {
    if (n > m) swap(n, m);
    int ans = 0, pos;
    for (int i = 1; i <= n; i = pos + 1) {
        pos = min(n / (n / i), m / (m / i));
        ans += (sum[pos] - sum[i - 1]) * (n / i) * (m / i);
    }
    return ans;
}

int main() {
    ios::sync_with_stdio(false);
    get_prefix();
    cin >> T;
    while (T--) {
        cin >> a >> b >> d;
        printf("%d\n", cal(a / d, b / d));
    }
    return 0;
}
```