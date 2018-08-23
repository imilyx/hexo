---
title: 'Codeforces Round 504 Div2'
date: 2018-08-23 18:00:40
tags: 
    - 比赛
mathjax: true
---

[比赛链接](http://codeforces.com/contest/1023)

### A. Single Wildcard Pattern Matching
-----

啊啊啊，这题被 hack 了很痛苦，重申题意后发现星号部分只能用小写英文字母代替。重新写了代码又交，交了两发，过不去就弃疗了。现在看来，是没有判断非星号部分是否完全相同的原因。

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 2e5 + 10;
int n, m;
string s1, s2;

int main() {
    scanf("%d%d", &n, &m);
    cin >> s1 >> s2;
    int flag = -1;
    for (int i = 0; i < n; i++) if (s1[i] == '*') {
        flag = i; break;
    }
    if (flag == -1 && s1 == s2) {
        puts("YES"); return 0;
    }
    if (flag == -1 || m < n - 1) {
        puts("NO"); return 0;
    }
    for (int i = 0; i < flag; i++) if (s1[i] != s2[i]) {
        puts("NO"); return 0;
    }
    for (int i = n - 1, j = m - 1; i > flag; i--, j--) if (s1[i] != s2[j]) {
        puts("NO"); return 0;
    }
    for (int i = 0; i < m; i++) if (s2[i] < 'a' || s2[i] > 'z') {
        puts("NO"); return 0;
    }
    puts("YES");
    return 0;
}
```


### B. Pair of Toys
-----

首先数据非常大，必须 O(1). 若所有方案都小于 n，那么答案就是 (k - 1) / 2.

否则在此基础上再减去 min(k - n - 1, (k - 1) / 2)。

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
ll n, k, ans;

int main() {
    scanf("%lld%lld", &n, &k);
    ll tmp = (k - 1) / 2;
    if (n < k) tmp -= min(k - n - 1, (k - 1) / 2);
    printf("%lld\n", tmp);
    return 0;
}
```


### C.Bracket Subsequence
-----

真是道假题。。。直接删去头 k / 2 个左括号和头 k / 2 个右括号就行了。