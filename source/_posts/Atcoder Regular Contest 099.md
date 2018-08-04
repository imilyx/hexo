---
title: 'Atcoder Regular Contest 099'
date: 2018-08-04 21:37:40
tags: 
    - 比赛
mathjax: true
---

https://arc099.contest.atcoder.jp/assignments

一如既往地简洁！

### C. Minimization
-----

题意：给定 n 的排列，每次选择连续的 k 个元素，赋为这 k 个元素的最小值，重复此操作，直到所有元素相同。

即所有元素都是 1 咯！

显然我们每次操作的区间都会包含 1。因此 $Ans = 1 + \lceil \frac{N - K}{K - 1} \rceil$。$O(1)$

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

int n, k, x;

int main() {
    ios::sync_with_stdio(false);
    cin >> n >> k;
    for (int i = 1; i <= n; i++) cin >> x;
    int ans = 1;
    n -= k;
    ans += n / (k - 1);
    n %= (k - 1);
    if (n) ans++;
    cout << ans << endl;
    return 0;
}
```