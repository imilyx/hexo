---
title: 'Atcoder Regular Contest 101'
date: 2018-08-26 21:02:40
tags: 
    - 比赛
mathjax: true
---

[比赛链接](https://arc101.contest.atcoder.jp/assignments)

### C. Candles
-----

分三种情况讨论：

1. 选的位置全部 >= 0。

2. 选的位置全部 <= 0。

3. 选的位置包含 < 0 的和 > 0 的。

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
ll n, k, a[100005], flag;
ll sum, ans;

int main() {
    scanf("%lld%lld", &n, &k);
    a[0] = -1;
    for (int i = 1; i <= n; i++) {
        scanf("%lld", &a[i]);
        if (a[i] >= 0 && a[i - 1] < 0) flag = i;
    }
    if (!flag) flag = n + 1;
    ans = 1e18;
    if (flag + k - 1 <= n) ans = min(ans, a[flag + k - 1]);
    if (flag - k > 0) ans = min(ans, -a[flag - k]);
    for (int i = 1; i < flag; i++) {
        int j = i + k - 1;
        if (j < flag || j > n) continue;
        ans = min(ans, min(-a[i] - a[i] + a[j], a[j] * 2 - a[i]));
    }
    printf("%lld\n", ans);
    return 0;
}
```


### D. Median of Medians
-----

我感觉我用了一个小时的思考证明了这题不是中位数。。。。（好像中位数最低复杂度也要 $n^2$ 呢）只能在不求解 m 的情况下求出中位数了。

观摩了大神的解题思路觉得很妙！转化非常自然，但是也很难想到。。。

首先二分最后答案 x（这一步想到了！｡ì _ í｡）

### **问题转化为：有多少个区间的中位数小于等于 x**

将区间中所有 <= x 的赋为 1，> x 的赋为 -1. 

### **问题转化为：有多少个区间至少有（区间长度/2 + 1）个 1**

### **等价于：有多少个区间的和是正数**

即，对于 1/-1 序列求前缀和 sum[], 若 sum[r] - sum[l - 1] > 0，则这就是一个符合要求的序区间。

那么对于所有的 sum[x] > sum[y], 如果再满足 x > y, 则 [y + 1, x] 就是一个合法区间。我们可以用树状数组求顺序对的方法解决此题。

若二分判定函数 chk(x) 的返回值为 true，仅当区间和为正数的区间数量 > n * (n + 1) / 4. 因为区间总数共有 n * (n + 1) / 2 个，（位置 0 也算进去），这些区间中位数组成的序列的中位数就要再除以 2.

code:
``` c++
#include <bits/stdc++.h>
#define lowbit(x) ((x) & (-(x)))
using namespace std;

typedef long long ll;
const int N = 1e5 + 10, inf = (int)1e9;
int n, a[N], sum[N * 10], C[N * 10];

void add(int x) {
    for (; x <= 2 * N; x += lowbit(x)) C[x]++;
}

ll query(int x) {
    ll ret = 0;
    for (; x; x -= lowbit(x)) ret += C[x];
    return ret;
}

bool chk(int x) {
    for (int i = 1; i <= 2 * N; i++) C[i] = 0;
    for (int i = 1; i <= n; i++) sum[i] = sum[i - 1] + (a[i] <= x ? 1 : -1);
    ll tot = 0;
    for (int i = 0; i <= n; i++) {
        tot += query(sum[i] + N - 1);
        add(sum[i] + N);
    }
    return tot > 1ll * n * (n + 1) / 4;
}

int main() {
    scanf("%d", &n);
    int l = inf, r = -inf;
    for (int i = 1; i <= n; i++) {
        scanf("%d", &a[i]);
        l = min(l, a[i]), r = max(r, a[i]);
    }
    while (l < r) {
        int mid = (l + r) >> 1;
        if (chk(mid)) r = mid;
        else l = mid + 1;
    }
    printf("%d\n", r);
    return 0;
}
```


### E. Ribbons on Tree
-----

这题还是很想弄懂。。。然而目前官方题解日文版看不懂，网上题解找不到。。。打算留坑待填了。