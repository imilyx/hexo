---
title: 'NOIP 2018 Training 3'
date: 2018-07-02 14:50:40
tags: 
    - 比赛
mathjax: true
---

这次比较简单，但暴露了一些问题，要记下来，比赛前复习一遍！

### A. 联考之旅
-------------

题意：求 1 ~ N 阶乘之和模 m。如 N = 4、m = 10007 时，答案为 33。

可以发现 m 很小而 n 很大（可以搞事情

再观察发现若 i ($i \in [1, N]$) >= m ，那么它模 m 一定为 0。  O(n) 求出。

CODE：
``` c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
ll n, mo, ans = 1;

int main() {
    scanf("%lld%lld", &n, &mo);
    n = min(n, mo - 1);
    ans = n;
    for (int i = n - 1; i > 1; i--) {
        ans = (((ans + 1) * i) % mo) % mo;
    }
    printf("%lld\n", (ans == 1 ? ans : ans + 1) % mo);
    return 0;
}
```
这道只拿了 50 分，就是那一步模 m 的没有想到。这一类题没有模 m 的限制就会很难做，有的话首先想到 m 取一个小数，找找规律。


### B. 堆叠干草捆
---------------

题意：有一个长度为 n 的序列，有 k 次询问，每次要求给区间 [A, B] 内的序列加一。

简单的差分约束（前缀和维护）。

CODE：
``` c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
ll n, k, f[1000005];

int main() {
    scanf("%lld%lld", &n, &k);
    for (int i = 1; i <= k; i++) {
        int a, b;
        scanf("%d%d", &a, &b);
        f[a]++, f[b + 1]--;
    }
    for (int i = 1; i <= n; i++) f[i] += f[i - 1];
    sort(f + 1, f + n + 1);
    printf("%lld\n", f[(n + 1) / 2]);
    return 0;
}
```


### C. 打扫卫生
-------------

题意：一天有 T 个时段，Mr Fu 正打算安排他的 N 个小伙伴来学校打扫计算机教室，每个小伙伴都有自己的空闲时间段[Si，Ei]，只能把空闲的小伙伴安排出来打扫卫生。而且，每个时间段必须有人在打扫。求最少需要动用多少个小伙伴参与打扫计算机教室。如果没有办法安排出合理的方案，就输出-1。

贪心的区间问题，按右端点排序，每次挑左端点小于当前右端点的区间中右端点最靠右的区间。

CODE：
``` c++
#include <bits/stdc++.h>
using namespace std;

int n, T, t = -1, mx, vis[25005];

struct node {
    int s, e;
}a[25005];

bool cmp(node a, node b) {
    return a.e == b.e ? a.s < b.s : a.e < b.e;
}

int main() {
    scanf("%d%d", &n, &T);
    for (int i = 1; i <= n; i++) {
        scanf("%d%d", &a[i].s, &a[i].e);
        if (a[i].s == 1 && a[i].e > mx) t = i, mx = a[i].e;
    }
    if (t < 0) {
        printf("-1\n");
        return 0;
    }
    swap(a[1], a[t]);
    a[n + 1].s = 1000005;
    sort(a + 2, a + n + 1, cmp);
    int pos = a[1].e, pos2 = 2, ans = 1, cnt = 0;
    while (pos < T) {
        bool flag = false;
        for (int i = n; i >= pos2; i--) {
            if (vis[i] == 1) continue;
            flag = true;
            if (a[i].s <= pos + 1) {
                ans++, pos = a[i].e, vis[i] = 1, pos2 = i;
                break;
            }
        }
        ++cnt;
        if ((flag == false && pos < T) || cnt > n) {
            printf("-1\n");
            return 0;
        }
    }
    printf("%d\n", ans);
    return 0;
}
```
当时写挂了一个字母。。居然都有90分。。


### D. 图书问题
-------------

题意：善于观察的 WM 童鞋发现一共有 M 种图书，每种图书都有 N 本，并且每本书都有一个编号(同一种类的图书编号相同，不同种类的图书编号也可能相同）。于是 WM 童鞋就开始思考，如果把这些书的编号进行组合，能组合出好多数字。问题也随之而来，用总数不超过 N 本书的编号进行组合，在组合出来的数字中可以连续出现的数字中最多的有多少个?

多重背包。可惜的是统计答案时错了，要注意的是连续出现的数字段左端点不一定是 1。

CODE：
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 205, M = 51005;
int n, m, a[N], used[M], ans, sum;
bool f[M];

int main() {
    scanf("%d%d", &n, &m);
    int mx = 0;
    for (int i = 1; i <= n; i++) {
        scanf("%d", &a[i]);
        mx = max(mx, a[i]);
    }
    memset(f, false, sizeof(f));
    f[0] = true, ans = 0;
    for (int i = 1; i <= n; i++) {
        for (int j = a[i]; j <= mx * m; j++) {
            if (!f[j] && f[j - a[i]] && used[j - a[i]] < m)
                f[j] = true, used[j] = used[j - a[i]] + 1;
            else if (f[j] && f[j - a[i]] && used[j - a[i]] < used[j] - 1)
                used[j] = used[j - a[i]] + 1;
        }
    }
    for (int i = 1; i <= mx * m; i++) {
        if (f[i]) sum++;
        else ans = max(ans, sum), sum = 0;
    }
    printf("%d\n", ans);
    return 0;
}
```

其实这次错得都是不应该的地方，（不错就AK了！） 应该都是能避免的错误。