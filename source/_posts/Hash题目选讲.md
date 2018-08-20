---
title: 'Hash题目选讲'
date: 2018-08-20 23:00:40
tags: 
    - Hash
mathjax: true
---

### [POJ3349]-Snowflake Snow Snowflakes
-----

题意：给你 n 个雪花，求解其中是否存在 2 个雪花从任意位置顺时针或逆时针形状是相同的。

定义 Hash 函数 $H(a_{i, 1}, a_{i, 2}, ... a_{i, 6}) = (\sum_{j = 1}^6a_{i, j} + \prod_{j = 1}^6a_{i, j}\ mod\ P$, 其中 P 是一个大质数。

code:
``` c++
#include <cstdio>
using namespace std;

typedef long long ll;
const int N = 1e5 + 10, P = 99991;
int n, tot, snow[N][10], head[N], next[N];

int Hash(int a[]) {
    int sum = 0, mul = 1;
    for (int i = 0; i < 6; i++) {
        sum = (sum + a[i]) % P;
        mul = (ll)mul * a[i] % P;
    } return (sum + mul) % P;
}

bool equal(int a[], int b[]) {
    for (int i = 0; i < 6; i++) {
        for (int j = 0; j < 6; j++) {
            bool eq = 1;
            for (int k = 0; k < 6; k++)
                if (a[(i + k) % 6] != b[(j + k) % 6]) eq = 0;
            if (eq) return 1;
            eq = 1;
            for (int k = 0; k < 6; k++)
                if (a[(i + k) % 6] != b[(j - k + 6) % 6]) eq = 0;
            if (eq) return 1;
        }
    }
    return 0;
}

bool insert(int a[]) {
    int val = Hash(a);
    for (int i = head[val]; i; i = next[i]) {
        if (equal(snow[i], a)) {
            return 1;
        }
    }
    ++tot;
    for (int i = 0; i < 6; i++) snow[tot][i] = a[i];
    next[tot] = head[val];
    head[val] = tot;
    return 0;
}

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) {
        int a[10];
        for (int j = 0; j < 6; j++) scanf("%d", &a[j]);
        if (insert(a)) {
            puts("Twin snowflakes found.");
            return 0;
        }
    }
    puts("No two snowflakes are alike.");
    return 0;
}
```


### 兔子与兔子
-----

题意：有 1 个DNA序列，每次询问其中的两段，求这两段是否相同。

裸的字符串Hash。需要了解的是，若字符串 S 的 Hash 值是 H(S), 字符串 S + T 的 Hash 值是 H(S + T), 那么字符串 T 的 Hash 值就是 $H(T) = (H(S + T) - H(S) * P^{length(T)})\ mod\ M$, 其中乘 P 就相当于 P 进制下的左移运算。

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

char s[1000010];
long long f[1000010], p[1000010];

int main() {
    int n, q;
    scanf("%d%d", &n, &q);
    scanf("%s", s + 1);
    p[0] = 1;
    for (int i = 1; i <= n; i++) {
        f[i] = f[i - 1] * 131 + (s[i] - 'a' + 1);
        p[i] = p[i - 1] * 131;
    }
    while (q--) {
        int l1, r1, l2, r2;
        scanf("%d%d%d%d", &l1, &r1, &l2, &r2);
        if (f[r1] - f[l1 - 1] * p[r1 - l1 + 1] == f[r2] - f[l2 - 1] * p[r2 - l2 + 1])
            puts("Yes");
        else puts("No");
    }
    return 0;
}
```


### [POJ3974]-Palindrome
-----

题意：寻找一个长度为 N 的字符串 S 的最长回文字串。

最长回文字串分为长度为奇数的和长度为偶数的，我们可以分情况讨论，正着倒着分别处理一遍 Hash 前缀和。假设最长回文字串的长度为 len，二分一半长度。最后取 max。时间复杂度 $O(N\ log\ N)$. 据说有个叫 Manacher 的算法可以 $O(N)$.

code:
``` c++
#include <cstdio>
#include <cstring>
#include <algorithm>
using namespace std;

typedef unsigned long long ull;
const int P = 131, N = 1e6 + 10;
ull p[N], h1[N], h2[N];
char s[N];
int len, Case;

bool chk(int l1, int r1, int l2, int r2) {
    ull val1 = h1[r1] - h1[l1 - 1] * p[r1 - l1 + 1];
    ull val2 = h2[r2] - h2[l2 + 1] * p[l2 - r2 + 1];
    return val1 == val2;
}

int main() {
    p[0] = 1;
    for (int i = 1; i < N; i++) p[i] = p[i - 1] * P;
    while (~scanf("%s", s + 1) && s[1] != 'E') {
        len = strlen(s + 1);
        h1[0] = h2[len + 1] = 0;
        for (int i = 1; i <= len; i++) h1[i] = h1[i - 1] * P + (s[i] - 'a' + 1);
        for (int i = len; i >= 1; i--) h2[i] = h2[i + 1] * P + (s[i] - 'a' + 1);
        int ans, l, r, maxn = 1;
        for (int i = 1; i <= len; i++) {
            l = 1, r = min(len - i, i - 1), ans = 0;
            while (l <= r) {
                int mid = (l + r) >> 1;
                if (chk(i - mid, i - 1, i + mid, i + 1))
                    l = mid + 1, ans = mid;
                else r = mid - 1;
            }
            maxn = max(maxn, 2 * ans + 1);

            if (s[i] != s[i + 1]) continue;
            l = 1, r = min(i - 1, len - (i + 1)), ans = 0;
            while (l <= r) {
                int mid = (l + r) >> 1;
                if (chk(i - mid, i - 1, i + 1 + mid, i + 2))
                    ans = mid, l = mid + 1;
                else r = mid - 1;
            }
            maxn = max(maxn, 2 * ans + 2);
        }
        printf("Case %d: %d\n", ++Case, maxn);
    }
    return 0;
}
```