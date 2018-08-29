---
title: 'Codeforces Round 502 Div2'
date: 2018-08-29 15:59:40
tags: 
    - 比赛
mathjax: true
---

[比赛链接](http://codeforces.com/contest/1017)

### A. The Rank
-----

水题，struct 排序。


### B. The Bits
-----

题意：给两个01字符串 a、b，只能交换第一个字符串中的两个字母，问有多少种交换方案使得交换后 a | b 不同于交换前的 a | b.

只需要考虑运算或的性质就好了。

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

int n, a1, a0;
typedef long long ll;
ll ans;
char a[100005], b[100005];

int main() {
    scanf("%d", &n);
    scanf("%s%s", a + 1, b + 1);
    for (int i = 1; i <= n; i++) {
        if (a[i] == '1') a1++;
        if (a[i] == '0' && b[i] == '1') a0++;
    }
    for (int i = 1; i <= n; i++) {
        if (a[i] == '1' && b[i] == '0') ans += a0;
        else if (a[i] == '0' && b[i] == '0') ans += a1;
    }
    cout << ans << endl;
    return 0;
}
```

### C. The Phone Number
-----

题意：给定 n，要求构造一个长度为 n 的序列使得这个序列的 LIS + LDS 长度最小。

分块思想，分成 k 块时答案为 n / k + k, 因此 k 取 sqrt(n) 最优。

也就是说图大致如下：
``` c++
            1
              1
                1
      1
        1
          1
1
  1
    1
```

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

int a[100005];

int main() {
    int n;
    scanf("%d", &n);
    int S = (int)sqrt(n + 0.5);
    int ps = 1;
    while (ps <= n) {
        for (int j = min(ps + S - 1, n); j >= ps; --j) a[++a[0]] = j;
        ps += S;
    }
    for (int i = 1; i <= a[0]; ++i) printf("%d ", a[i]); putchar('\n');
    return 0;
}
```