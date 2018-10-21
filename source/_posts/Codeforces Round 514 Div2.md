---
title: 'Codeforces Round 514 Div2'
date: 2018-10-21 14:36:40
tags: 
    - 比赛
mathjax: true
---

[比赛链接](http://codeforces.com/contest/1059)

这场因为第一题题意不懂，就卡在第一题上了，导致爆零。

### A. Cashier
-----

这题的意思是两个时间段之间能休息多少个a。

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 1e5 + 10;
int l[N], r[N], n, L, a;

int main() {
    cin >> n >> L >> a;
    long long ret = 0;
    for (int i = 1; i <= n; i++) {
        cin >> l[i] >> r[i];
        r[i] += l[i];
        ret += (l[i] - r[i - 1]) / a;
    }
    ret += (L - r[n]) / a;
    cout << ret << endl;
    return 0;
}
```

### B. Forgery
-----

B题需要转化一步。我们可以顺思逆想：对于 (i, j)，若它的八个方向都为 #，表示 (i, j) 这个位置有一步染色操作；否则没有。

code:
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 1000 + 10;
const int d[8][2] = {0, 1, 0, -1, 1, 0, -1, 0, 1, 1, 1, -1, -1, 1, -1, -1};
int n, m;
char s[N][N], a[N][N];

bool chk(int x, int y) {
    rep(i, 0, 7) if (s[x + d[i][0]][y + d[i][1]] != '#') return false;
    return true;
}

int main() {
    cin >> n >> m;
    for (int i = 1; i <= n; i++) cin >> (s[i] + 1);
    rep(i, 1, n) rep(j, 1, m) a[i][j] = '.';
    rep(i, 1, n)
        rep(j, 1, m)
            if (chk(i, j))
                rep(k, 0, 7) a[i + d[k][0]][j + d[k][1]] = '#';
    rep(i, 1, n)
        rep(j, 1, m)
            if (a[i][j] != s[i][j]) {
                puts("NO"); return 0;
            }
    puts("YES");
    return 0;
}
```

### C. Sequence Transformation
-----

因为两个奇数的gcd肯定是1，我们第一轮要先删掉所有奇数，然后接下来每一轮都删掉2的幂次方，这样使gcd序列变大的速度是最快的，而且增大的幅度也是最大的。

唯一一个特例就是 = 3的时候，是 1 1 3.

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n;
    cin >> n;
    for (int i = 1; i <= n; i <<= 1) {
        if (i * 4 > n) {
            if (i * 2 > n) printf("%d\n", i);
            else if (i * 3 > n) printf("%d %d\n", i, i * 2);
            else printf("%d %d %d\n", i, i, i * 3);
            break;
        }
        for (int j = i; j <= n; j += (i << 1)) printf("%d ", i);
    }
    return 0;
}
```