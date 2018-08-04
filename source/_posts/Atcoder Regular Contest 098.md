---
title: 'Atcoder Regular Contest 098'
date: 2018-08-04 21:38:40
tags: 
    - 比赛
mathjax: true
---

### C. Attention
-----

很简单的前缀和，不讲，

### D. Xor Sum 2
-----

由题意可以得到，根据xor的消去律，连续子序列，如果 $a_i$ 这个数不符合，之后的对数中将不再需要这个元素，即从 $a_{i + 1}$ 开始。根据消去律可得，异或等效于加法与减法，可据此计算区间的异或和。

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
const int N = 2e5 + 10;
ll n, f[N], s[N], x, ans;

int main() {
    ios::sync_with_stdio(false);
    cin >> n;
    for (int i = 1; i <= n; i++) {
        cin >> x;
        f[i] = f[i - 1] + x;
        s[i] = s[i - 1] ^ x;
    }
    int l = 1;
    for (int r = 1; r <= n; r++) {
        for (; f[r] - f[l - 1] != (s[r] ^ s[l - 1]); l++);
        ans += r - l + 1;
    }
    cout << ans << endl;
    return 0;
}
```