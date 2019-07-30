---
title: 'Educational Codeforces Round 65'
date: 2019-07-30 16:15:40
tags: 
    - 比赛
mathjax: true
---

[传送门](http://codeforces.com/contest/1167)

### A. Telephone Number
-----

签到题

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

int T, n;
char s[105];

int main() {
    cin >> T;
    while (T--) {
        cin >> n;
        scanf("%s", s + 1);
        if (n > 11) {
            int pos = 0;
            rep(i, 1, n)
                if (s[i] == '8') { pos = i; break; }
            if (n - (pos - 1) < 11 || !pos) puts("NO");
            else puts("YES");
        } else if (n == 11) {
            if (s[1] != '8') puts("NO");
            else puts("YES");
        } else puts("NO");
    }
    return 0;
}
```

### B. Lost Numbers
-----

第一次 B 题是交互诶。。

很暴力

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int a[10] = {0, 4, 8, 15, 16, 23, 42};
int x, y, z, w, ans[10];

bool find(int x) {
    rep(i, 1, 6) if (a[i] == x) return 1;
    return 0;
}

int main() {
    printf("? 1 2\n"); fflush(stdout); cin >> x;
    printf("? 3 4\n"); fflush(stdout); cin >> y;
    printf("? 1 3\n"); fflush(stdout); cin >> z;
    printf("? 1 5\n"); fflush(stdout); cin >> w;

    int tot = 1;
    rep(i, 1, 6) tot *= a[i];

    rep(i, 1, 6) {
        if (x % a[i] == 0 && z % a[i] == 0 && w % a[i] == 0 && find(x / a[i]) && find(z / a[i]) && find(w / a[i])) {
            ans[1] = a[i];
            ans[2] = x / a[i];
            ans[3] = z / a[i];
            ans[4] = y / ans[3];
            ans[5] = w / a[i];
            ans[6] = tot / x / y / ans[5];
            break;
        }
    }
    printf("! %d %d %d %d %d %d\n", ans[1], ans[2], ans[3], ans[4], ans[5], ans[6]);
    fflush(stdout);
    return 0;
}
```

### C. News Distribution
-----

题目看到一半。。嗯？？冰茶姬？？？

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 5e5 + 10;
int n, m, a[N], fa[N], sum[N];

int getfa(int w) {
    return fa[w] == w ? w : fa[w] = getfa(fa[w]);
}

void merge(int x, int y) {
    x = getfa(x), y = getfa(y);
    if (x != y)
        fa[x] = y, sum[y] += sum[x];
}

int main() {
    cin >> n >> m;
    rep(i, 1, n) fa[i] = i, sum[i] = 1;
    rep(i, 1, m) {
        scanf("%d", &a[i]);
        int lst = 0;
        rep(j, 1, a[i]) {
            int x; scanf("%d", &x);
            if (lst) merge(x, lst);
            lst = x;
        }
    }
    rep(i, 1, n) printf("%d ", sum[getfa(i)]);
    return 0;
}
```

### D. Bicolored RBS
-----

比较暴力的思想：维护红色和蓝色括号序列的前缀和，然后 '(' 优先给前缀和小的，')' 优先给前缀和大的

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 2e5 + 10;
int n, a[N], ans[N];
char s[N];

int main() {
    cin >> n;
    scanf("%s", s + 1);
    int x = 0, y = 0;
    rep(i, 1, n) {
        if (s[i] == '(') {
            if (x < y) x++, ans[i] = 0;
            else y++, ans[i] = 1;
        } else {
            if (x > y) x--, ans[i] = 0;
            else y--, ans[i] = 1;
        }
    }
    rep(i, 1, n) printf("%d", ans[i]);
    return 0;
}
```

### E. Range Deleting
-----

赛后补题！挺妙滴

发现，删除的区间若左区间固定，右区间呈单调性。设对于不同的左区间 l_i, 都有右区间 r_i, 则 r_1 <= r_2 <= ... <= r_n, 所以可以用二分或 two-pointers 找 r_i.

可以预处理每个 a_i 最左和最右的位置，预处理 1 ~ i 最右的位置 和 i ~ x 最左的位置，用于在查找 r_i 时判断某部分数是否呈非递减。

若区间 [l, r] 都可以删除，那么 [l, r + 1] 也可以删除，所以此时对答案产生贡献 (n - r + 1).

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 1e6 + 10;
typedef long long ll;
int n, x, a[N], l[N], r[N], L[N], R[N];

int main() {
    cin >> n >> x;
    memset(l, 0x3f, sizeof(l));
    memset(L, 0x3f, sizeof(L));
    rep(i, 1, n) {
        scanf("%d", &a[i]);
        l[a[i]] = min(l[a[i]], i);
        r[a[i]] = i;
    }
    for (int i = x; i >= 1; i--)
        L[i] = min(L[i + 1], l[i]);
    rep(i, 1, x)
        R[i] = max(R[i - 1], r[i]);
    int k = x;
    for (; k > 1 && r[k] <= L[k + 1]; --k);  // 要删除的区间是 [1, k]
    ll ans = x - k + 1;  // 产生贡献
    for (int i = 2; i <= x && R[i - 2] <= l[i - 1]; i++) {
        for (; k < i || R[i - 1] > L[k + 1]; ++k);  // 要删除的区间是 [i, k]
        ans += x - k + 1;
    }
    printf("%lld\n", ans);
    return 0;
}
```

### F. Scalar Queries
-----

因为是 vp 的，，到点儿了就要吃饭去，，抵不住十三香大虾🍤的诱惑 [捂脸] F 题只是有了初步思路，，，，，

后来是看题解的。。好题。。。

变相理解题意就是：如果 a 和 b 在一个区间 [l, r] 里且 a < b, 那么就会对 f(l, r) 产生 b 的贡献（a 和 b 对 f(l, r) 的初始贡献分别是 a 和 b

* a[i] 和 a[j] 在一个区间的情况数 共有 $i * (n - j + 1)$ 种

如果在 a[i] 的前面有个比 a[i] 小的数 a[j]，那么 a[j] 对 a[i] 系数的贡献应该等于包含 a[i] 和 a[j] 的区间数量，即 $j*(n-i+1)$

如果在 a[i] 的前面有个比 a[i] 小的数 a[j]，同上，贡献等于 $i*(n-j+1)$

所以我们现在需要维护比 a[i] 小的数的信息，那么对 a[i] 权值排序，可以用两个树状数组实现，分别维护上述两种情况。

``` c++
一点儿收获：对于这样的题，肯定要 O(n) 或 O(n log n) 解决，只能数据结构出场，比如树状数组。为了保持 “值小的优先处理” 的顺序，我们需要按照值的大小排序。
```

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
#define lowbit(x) ((x) & (-(x)))
using namespace std;

const int N = 5e5 + 10, mod = 1e9 + 7;
typedef long long ll;
struct node {
    ll w, id;
}a[N];
int n;

bool cmp(node a, node b) { return a.w < b.w; }

struct BIT {
    int C[N];

    void add(int x, ll val) {
        for (; x <= n; x += lowbit(x)) {
            C[x] += val;
            if (C[x] >= mod) C[x] -= mod;
        }
    }

    ll query(int x) {
        ll ret = 0;
        for (; x; x -= lowbit(x)) {
            ret += C[x];
            if (ret >= mod) ret -= mod;
        }
        return ret;
    }
}l, r;

int main() {
    cin >> n;
    rep(i, 1, n) {
        scanf("%lld", &a[i].w);
        a[i].id = i;
    }
    sort(a + 1, a + n + 1, cmp);
    ll ans = 0;
    rep(i, 1, n) {
        ll sum = a[i].id * (n - a[i].id + 1) % mod;
        sum = (sum + l.query(a[i].id) * (n - a[i].id + 1) % mod) % mod;
        sum = (sum + (r.query(n) - r.query(a[i].id) + mod) % mod * a[i].id % mod) % mod;
        l.add(a[i].id, a[i].id);
        r.add(a[i].id, n - a[i].id + 1);
        ans = (ans + a[i].w * sum % mod) % mod;
    }
    printf("%lld\n", ans);
    return 0;
}
```