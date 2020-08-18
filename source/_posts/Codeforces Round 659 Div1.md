---
title: 'Codeforces Round 659 Div1'
date: 2020-08-15 14:31:40
tags: 
    - 比赛
mathjax: true
---

最近的场子，做一下。

## A
-----

贪心就好了？也可以并查集维护。

``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 1e5 + 10;
int T, n, num[30][30];
char a[N], b[N];

int main() {
    cin >> T;
    while (T--) {
        cin >> n;
        scanf("%s%s", a + 1, b + 1);
        bool ff = 1;
        rep(i, 1, n) if (a[i] > b[i]) { ff = 0; break; }
        if (!ff) { puts("-1"); continue; }
        memset(num, 0, sizeof(num));
        rep(i, 1, n) {
            if (a[i] != b[i])
                num[a[i] - 'a'][b[i] - 'a']++;
        }
        int ans = 0;
        rep(i, 0, 25) {
            rep(j, i + 1, 25) {
                if (num[i][j]) {
                    ++ans;
                    num[i][j] = 0;
                    rep(k, j + 1, 25) {
                        if (num[i][k]) {
                            num[j][k] += num[i][k], num[i][k] = 0;
                        }
                    }
                }
            }
        }
        printf("%d\n", ans);
    }
    return 0;
}
```

## B
-----

发现如果有一位 1 的个数是奇数就必然不会平局，然后分类讨论就好了。

## C
-----

C 和 A 的区别在于 C 有环。

## D
-----

## E
-----

