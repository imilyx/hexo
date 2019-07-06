---
title: 'Codeforces Round 572 Div2'
date: 2019-07-05 19:30:40
tags: 
    - 比赛
mathjax: true
---

[比赛链接](http://codeforces.com/contest/1189)

VP 的～

### A. Keanu Reeves
-----

H2O 签到题～

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

int n, a[105], b[105], c[105];
char s[105];

int main() {
    scanf("%d", &n);
    scanf("%s", s + 1);
    for (int i = 1; i <= n; i++) {
        c[i] = s[i] - '0';
        a[i] = a[i - 1], b[i] = b[i - 1];
        if (c[i]) a[i]++; else b[i]++;
    }
    if (a[n] != b[n]) {
        puts("1");
        rep(i, 1, n) printf("%d", c[i]);
        puts("");
    } else {
        puts("2");
        for (int i = 1; i <= n; i++) {
            if (a[i] != b[i]) {
                rep(j, 1, i) printf("%d", c[j]);
                printf(" ");
                rep(j, i + 1, n) printf("%d", c[j]);
                puts("");
                return 0;
            }
        }
    }
    return 0;
}
```

### B. Number Circle
-----

尽量保持 大->小->大 的顺序～

code :
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 1e5 + 10;
typedef long long ll;
int n;
ll a[N], b[N];

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) {
        scanf("%lld", &a[i]);
    }
    sort(a + 1, a + n + 1);
    int l = n + 1, r = 1;
    b[1] = a[1];
    for (int i = 2; i <= n; i++) {
        if (i % 2 == 0) b[++r] = a[i];
        else b[--l] = a[i];
    }

    for (int i = 1; i <= n; i++) {
        int l = i - 1, r = i + 1;
        if (!l) l = n;
        if (r > n) r = 1;
        if (b[l] + b[r] <= b[i]) {
            puts("NO"); return 0;
        }
    }
    puts("YES");
    for (int i = 1; i <= n; i++) printf("%d ", b[i]);
    puts("");
    return 0;
}
```

### C. Candies!
-----

当时暴力模拟是 T 了一发，然后才想到 ST 表。。（这种题应该首选 ST 啊！

不是很难，复习一波！

code :
``` c++
#include <bits/stdc++.h>
using namespace std;

const double eps = 1e-8;
const int N = 1e5 + 10;
int a[N], n, q, l, r, pre[N], ans, f[N][20], g[N][20];

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d", &f[i][0]);

    int tmp = log(n + eps) / log(2);
    for (int j = 1; j <= tmp; j++) {
        int len = (1 << (j - 1));
        int k = n - (1 << j) + 1;
        for (int i = 1; i <= k; i++) {
            f[i][j] = (f[i][j - 1] + f[i + len][j - 1]) % 10;
            g[i][j] = (g[i][j - 1] + g[i + len][j - 1]) + (f[i][j - 1] + f[i + len][j - 1] >= 10);
        }
    }

    cin >> q;
    while (q--) {
        scanf("%d%d", &l, &r);
        tmp = log(r - l + 1 + eps) / log(2);
        printf("%d\n", g[l][tmp]);
    }
    return 0;
}
```

### D1. Add on a Tree
-----

发现，有 3 个以上分支的点，它连接的边可以任意+。

所以如果有 2 个分支的点就不可以！

code :
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 1e5 + 10;
int n, d[N];

int main() {
    scanf("%d", &n);
    for (int i = 1; i < n; i++) {
        int x, y; scanf("%d%d", &x, &y);
        d[x]++, d[y]++;
    }
    bool ff = 0;
    for (int i = 1; i <= n; i++) if (d[i] == 2) { ff = 1; break; }
    if (ff) puts("no");
    else puts("yes");
    return 0;
}
```

### E. Count Pairs
-----

这种题肯定得 i 归一边，j 归另一边，各管各的才好～

因式分解党表示强迫症发作～

两边同乘 (ai - aj)，就是 ai^4 - aj^4 恒等于 (ai - aj)k (mod p)

整理得 ai^4 - aiK 恒等于 aj^4 - ajK (mod p)

那么就很 easy 惹～ E题 AC～

code :
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 3e5 + 10;
typedef long long ll;
int n, mod, K, a[N], ans;
map<int, int> mp;

int sub(int a, int b) {
    int t = a - b;
    if (t < 0) t += mod;
    return t;
}

int mul(int a, int b) {
    return (1ll * a * b) % mod;
}

int p(int x) {
    return mul(mul(x, x), mul(x, x));
}

int main() {
    cin >> n >> mod >> K;
    for (int i = 1; i <= n; i++) {
        scanf("%lld", &a[i]);
        a[i] = sub(p(a[i]), mul(K, a[i]));
        ans += mp[a[i]];
        mp[a[i]]++;
    }
    printf("%d\n", ans);
    return 0;
}
```

### 总结
-----

有进步！暑假拼了 (ง •̀灬•́)ง