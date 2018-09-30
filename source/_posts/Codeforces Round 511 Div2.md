---
title: 'Codeforces Round 511 Div2'
date: 2018-09-24 19:36:40
tags: 
    - 比赛
mathjax: true
---

[比赛链接](https://codeforces.com/contest/1047)

emmmm，这次做了两道，但由于手速脑速不够以及 WA 了几发，成功凉凉，，，，

感觉很活！！要多打！！

### A. Little C Loves 3 I
-----

啊，有个很清楚的做法：a = b = c = n / 3（下取整），如果 b % 3 == 0 或 c % 3 == 0, a++, b/c --.

这样就先保证了 b 和 c 不是 3 的倍数，然后再处理 a，判断 b 和 c % 3 = 1 还是 2 就行了。

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

int n, a[5];

int main() {
    scanf("%d", &n);
    a[0] = a[1] = a[2] = n / 3;
    int sum = n - (n / 3) * 3;
    a[0] += sum;
    if (a[1] % 3 == 0) a[1]--, a[0]++;
    if (a[2] % 3 == 0) a[2]--, a[0]++;
    if (a[0] % 3 == 0) {
        if (a[1] % 3 == 1) a[1]++, a[0]--;
        else if (a[2] % 3 == 1) a[2]++, a[0]--;
        else if (a[1] % 3 == 2) a[1]--, a[0]++;
        else if (a[2] % 3 == 2) a[2]--, a[0]++;
    }
    printf("%d %d %d\n", a[0], a[1], a[2]);
    return 0;
}
```

### B. Cover Points
-----

可以发现，答案就是 $max_{1 \leq i \leq n}\{x_i + y_i\}$.

### C. Enlarge GCD
-----

要看清题目要求，只要比整体gcd大就行了。

设整体gcd为 G。对于每个 > G 的质数 x，记 cnt 为 ai 中 % x = 0 的数的个数，ans 对 n - cnt 取 min。

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
const int N = 1.5e7 + 10;
int n, a[N], G, x;
bool pri[N];

int gcd(int a, int b) {
    return !b ? a : gcd(b, a % b);
}

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) {
        scanf("%d", &x);
        a[x]++;
        if (!G) G = x;
        else G = gcd(G, x);
    }
    int ans = n;
    for (int i = G + 1; i < N; i++) {
        if (!pri[i]) {
            int cnt = 0;
            for (int j = i; j < N; j += i)
                pri[j] = 1, cnt += a[j];
            ans = min(ans, n - cnt);
        }
    }
    if (ans < n) cout << ans << endl;
    else cout << -1 << endl;
    return 0;
}
```

### D. Little C Loves 3 II
-----

首先，$1 * 6$, $2 * 4$, $2 * 5$, $3 * 4$ 都是能被构造出来的。

如果 n = 1， 那么答案就是 $6 * \lfloor \frac{m}{6} \rfloor + 2 * max((m\ mod\ 6) - 3, 0)$.

如果 n = 2，只有 $2 * 2$，$2 * 3$ 和 $2 * 7$ 不能被构造，其他的都可以用 $1 * 6$，$2 * 4$ 和 $2 * 5$ 构造出来。

否则，$4 * x$ 可以用 $2 * 4$ 和 $3 * 4$ 构造，$6 * x$ 可以用 $1 * 6$ 构造。$x * y$ 可以用 $4 * x$ 和 $6 * y$ 构造，其中 y 是偶数且 x、y > 2.

也就是说，如果 $n * m\ mod\ 2 = 0$, 那么答案就是 $n * m$. 否则，我们可以用一些 $x * y$ 的矩阵（其中 x 或 y 是偶数）将 $n * m$ 的矩阵变为如下三个之一： $3 * 3$, $3 * 5$, $5 * 5$。而这三个都是有一个空的。即答案为 $n * m - 1$.

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
int n, m, ans;

int main() {
    scanf("%d%d", &n, &m);
    if (n > m) swap(n, m);
    if (n == 1) printf("%d\n", (m / 6 * 3 + max(m % 6 - 3, 0)) << 1);
    else if (n == 2) printf("%d\n", m == 2 ? 0 : m == 3 ? 4 : m == 7 ? 12 : m << 1);
    else printf("%lld\n", 1ll * n * m / 2 * 2);
    return 0;
}
```