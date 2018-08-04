---
title: 'Atcoder Regular Contest 100'
date: 2018-08-04 21:36:40
tags: 
    - 比赛
mathjax: true
---

https://arc100.contest.atcoder.jp/assignments

话说 atcoder 的题目都超短易懂有没有！

### C. Linear Approximation
-----

题意略。

$A_i - (b + i)$ 几何意义就是 $A_i - i$ 在数轴上到 b 的距离啦。

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
ll n, a[200005], num, tot;

ll iabs(ll a) {return a > 0 ? a : -a;}

int main() {
    ios::sync_with_stdio(false);
    cin >> n;
    for (int i = 1; i <= n; i++) cin >> a[i], a[i] -= i;
    sort(a + 1, a + n + 1);
    num = a[(n + 1) >> 1];
    for (int i = 1; i <= n; i++) tot += iabs(a[i] - num);
    cout << tot << endl;
    return 0;
}
```

### D. Equal Cut
-----

题意：给你 N 个数，存为 Ai，切三刀，分为四组，每一组的和分别为 b、c、d、e，要求这四个数的极差（最大数和最小数的差）最小，输出这个最小值。

先预处理出前缀和，暴力中间的断点，在循环中找一前一后两个断点，如果移动能使得前后两个区域之间的差变小便移动，否则不移动。用一个变量存储所有循环中间断点时出现的最小值，输出即可。

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 2e5 + 5;
typedef long long ll;
ll n, a[N], pre[N], t[5], ans = 1e9;

ll iabs(ll a) {return a > 0 ? a : -a;}

int main() {
    ios::sync_with_stdio(false);
    cin >> n;
    for (int i = 1; i <= n; i++) cin >> a[i], pre[i] = pre[i - 1] + a[i];
    int l = 1, r = 3;
    for (int i = 2; i < n - 1; i++) {
        while (l < i && iabs(pre[l] - pre[0] - (pre[i] - pre[l])) >= iabs(pre[l + 1] - pre[0] - (pre[i] - pre[l + 1])))
            l++;
        while (r < n && iabs(pre[r] - pre[i] - (pre[n] - pre[r])) >= iabs(pre[r + 1] - pre[i] - (pre[n] - pre[r + 1])))
            r++;
        t[1] = pre[l] - pre[0];
        t[2] = pre[i] - pre[l];
        t[3] = pre[r] - pre[i];
        t[4] = pre[n] - pre[r];
        ans = min(ans, max(t[1], max(t[2], max(t[3], t[4]))) - min(t[1], min(t[2], min(t[3], t[4]))));
    }
    cout << ans << endl;
    return 0;
}
```