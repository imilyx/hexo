---
title: '[CF-Gym-101775B]-Scapegoat'
date: 2018-06-17 14:50:40
tags: 
    - 数学
mathjax: true
---

https://vjudge.net/problem/Gym-101775B

题意：有 N 个大点，M 个小点，每个大点可以连多个小点，且至少得连一个小点，每个小点必须被一个大点连。每个大点有一个权值 $c_i$，被这个大点连的小点的权值之和即为大点权值。需要求出怎么连，在满足题意的情况下小点权值方差最小？
Hint：方差公式：

$$\sigma^2 = \frac{1}{n}\sum(x_i - \frac{1}{n}\sum x_j)^2$$

真是惭愧 ◑﹏◐ 当时题意都没读懂...
可以发现，小点这么求：

$$\sigma^2 = \frac{1}{m}\sum(x_i - \frac{1}{m}\sum x_j)^2$$

可以发现，小点的总权值之和即为大点总权值之和，是一个已知的常数，我们用 S 表示。
完全平方公式得：

$$\sigma^2 = \frac{1}{m}\sum({x_i}^2 - \frac{2}{m}Sx_i + \frac{1}{m^2}S^2)$$

拆开来化简得：

$$\frac{1}{m}\sum{x_i}^2 - \frac{S^2}{m^2}$$

由于 $\frac{1}{m}$ 、 $\frac{S^2}{m^2}$ 全是常数，可以直接得到，我们就不去考虑，现在要解决 $\sum{x_i}^2$ 的问题。

实在是太难考虑了 ◑﹏◐ 那换一个角度吧

我们发现，一个大点连着小点，每个小点获得的大点分出的权值肯定是越均匀越好（均分咯），设大点权值为 $C$ ，小点个数为 $M$ ，则 $x_i$ 为 $\frac{C}{M}$ 。

$\sum{x_i}^2$ 则为 $M(\frac{C}{M})^2$ 即为 $\frac{C^2}{M}$ 。

$C^2$ 我们也知道了，现在我们考虑的局部问题就是不同大点的小点数。对于每个大点来说，$M$ 肯定是越大越好咯，但为了让对答案的减少量更多，我们要考虑的是哪一个大点的 M 变大对答案的贡献越多。

仔细观察发现，$\frac{C^2}{1}$ 变为 $\frac{C^2}{2}$ 的减少量是 $\frac{C^2}{2}$ ，$\frac{C^2}{2}$ 变为 $\frac{C^2}{3}$ 的减少量是 $\frac{C^2}{2} - \frac{C^2}{3}$ ，也就是说减少量在递减，而我们却要选减少量将会最多的一个，这是一个很优的性质。

最终做法，每个大点的 M 初始是 1，循环 $m - n$ 次，用优先队列给准减少量降序排列，每次取队首，给它的 M 加1。

最后别忘了加上之前为了方便思考减去的一堆常数。

CODE：
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 2e5 + 5;
typedef pair<double, int> pdi;
int T, n, m;
double a[N];

priority_queue<pair<double, int> > pq;

int main() {
    scanf("%d", &T);
    for (int cas = 1; cas <= T; cas++) {
        scanf("%d%d", &n, &m);
        double ans = 0, S = 0;
        for (int i = 1; i <= n; i++) {
            scanf("%lf", &a[i]);
            S += a[i];
            pq.push(make_pair(a[i] * a[i] / 2, 1));
        }
        for (int i = n + 1; i <= m; i++) {
            pdi x = pq.top();
            pq.pop();
            double square = x.first * (x.second * (x.second + 1));
            x.second += 1;
            x.first = (square / x.second) - (square / (x.second + 1));
            pq.push(x);
        }
        while (!pq.empty()) {
            pdi x = pq.top();
            pq.pop();
            ans += x.first * x.second * (x.second + 1) / x.second;
        }
        ans /= m;
        ans -= S * S / m / m;
        printf("Case #%d: %.12lf\n", cas, max(0.0, ans));
    }
    return 0;
}
```

总结，本题在于展开，然后省略易求的常数，还有一个减少量的规律，也就是逐渐减小，逐渐不符合我们的要求。此外最可借鉴的就是思考策略——局部问题解法推广到整体，比如这道题中很难考虑时我们跳出去，先去发现减少量这个有趣的性质。