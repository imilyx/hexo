---
title: 'Codeforces Round 561 Div2'
date: 2019-07-28 23:30:40
tags: 
    - 比赛
mathjax: true
---

[传送门](http://codeforces.com/contest/1166)

### A. Silent Classroom
-----

签到题

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

int n, num[30], ans;
char s[30];

int main() {
    cin >> n;
    rep(i, 1, n) scanf("%s", s + 1), num[s[1] - 'a']++;
    rep(i, 0, 25) {
        int x = num[i] / 2, y = num[i] - x;
        ans += (x * (x - 1) / 2) + (y * (y - 1) / 2);
    }
    printf("%d\n", ans);
    return 0;
}
```

### B. All the Vowels Please
-----

题意：每行每列都得有 a e i o u

稍微构造下就好啦

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

int n, x, y;

int main() {
    cin >> n;
    for (int i = 2; i * i <= n; i++) if (n % i == 0) x = i, y = n / i;
    if (x < 5 || y < 5) { puts("-1"); return 0; }

    rep(i, 1, x) {
        if (i % 5 == 1) {
            printf("aeiou");
            rep(j, 1, y - 5) printf("a");
        } else if (i % 5 == 2) {
            printf("eioua");
            rep(j, 1, y - 5) printf("e");
        } else if (i % 5 == 3) {
            printf("iouae");
            rep(j, 1, y - 5) printf("i");
        } else if (i % 5 == 4) {
            printf("ouaei");
            rep(j, 1, y - 5) printf("o");
        } else if (i % 5 == 0) {
            printf("uaeio");
            rep(j, 1, y - 5) printf("u");
        } 
    }
    return 0;
}
```

### C. A Tale of Two Lands
-----

推不等式

(x2 = x^2)

min(|x - y|, |x + y|) <= |x|, |y| <= max(|x - y|, |x + y|)

|x|2 + |y|2 - 2|x||y| <= |x|2, |y|2 <= |x|2 + |y|2 + 2|x||y|

2|x| >= |y|, |x| >= -2|y|

总而言之就是 2|x| >= |y|, upper_bound() 搞一搞就 AC，，

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 2e5 + 10;
typedef long long ll;
int n, a[N];
ll ans;

int main() {
    cin >> n;
    rep(i, 1, n) scanf("%d", &a[i]), a[i] = fabs(a[i]);
    sort(a + 1, a + n + 1);
    rep(i, 1, n) {
        int tmp = upper_bound(a + 1, a + n + 1, 2 * a[i]) - a;
        if (tmp > n) tmp = n;
        if (a[tmp] > 2 * a[i]) tmp--;
        ans += tmp - i;
    }
    printf("%lld\n", ans);
    return 0;
}
```

### D. Cute Sequences
-----

有趣又恶心的构造！是赛后补的

主要看 [这位大佬](https://www.cnblogs.com/megalovania/p/10888493.html) 的，这边补充说明下：

* r_i 对 x_j (j >= i) 的贡献我们发现是 1，2，4，8，... 这样的

* 此处的贡献可以理解为：在位置 i 加一，位置 j (j >= i) 会加上多少？

* 在 “减回来” 的时候，我们从数列中靠前的位置开始，因为越靠前，对后面的影响力越大

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 55;
typedef long long ll;
int n, q;
ll l[N], r[N], prel[N], prer[N], pre[N];

int main() {
    pre[0] = 1;
    rep(i, 1, 50) rep(j, 0, i - 1) pre[i] += pre[j];  // 预处理影响

    cin >> q;
    while (q--) {
        ll a, b, m;
        cin >> a >> b >> m;
        prel[1] = l[1] = prer[1] = r[1] = a;
        if (a == b) {
            printf("1 %lld\n", a); continue;
        }
        for (int i = 2; ; i++) {
            l[i] = prel[i - 1] + 1;
            r[i] = prer[i - 1] + m;
            prel[i] = prel[i - 1] + l[i];
            prer[i] = prer[i - 1] + r[i];
            if (r[i] >= b) { n = i; break; }
        }
        if (l[n] > b) { puts("-1"); continue; }
        printf("%d ", n);
        rep(i, 2, n)
            if (r[n] > b) {
                ll d = min(m - 1, (r[n] - b) / pre[n - i]);
                rep(j, i, n) r[j] -= d * pre[j - i];
            }
        rep(i, 1, n) printf("%lld ", r[i]); puts("");
    }
    return 0;
}
```

### E. The LCMs Must be Large
-----

结论题好哇！

很容易证明，如果有两天的购买物品的集合完全不同，答案不存在：lcm(Si) > lcm(U - Si) >= lcm(Sj) > lcm(U - Sj) >= lcm(Si) （其中 Sx 表示一个集合） 这是不可能滴！

如何证明若任意两天购买集合都有交集，则必然存在答案呢？可以这样：先把数组 a 置为 1，把 a 中第一天购买集合乘以质数 p1，把 a 中第二天购买集合乘以质数 p2，就是一组符合要求的数字。

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

int n, m, s[55], mark[55][10010];

int main() {
    cin >> n >> m;
    rep(i, 1, n) {
        cin >> s[i];
        rep(j, 1, s[i]) {
            int x; scanf("%d", &x);
            mark[i][x] = 1;
        }
        rep(j, 1, i - 1) {
            int cnt = 0;
            rep(k, 1, m) if (mark[i][k] && mark[j][k]) cnt++;
            if (!cnt) { puts("impossible"); return 0; }
        }
    }
    puts("possible");
    return 0;
}
```