---
title: 浙大ACM第三轮选讲
date: 2020-07-22 16:56:40
tags:
mathjax: true
---

[7.21 题解](https://www.kdocs.cn/l/srN6cUALrHvX?f=201)

## [7.21 A]
-----

是斐波那契数列啊啊啊啊！所以当然 S(i, j) + S(i, j + 1) = S(i, j + 2)，精妙。

但是后面的矩阵设计就不知道了，咕咕。

upd: 还有另一种[神仙做法](https://www.cnblogs.com/Flying2018/p/13356497.html)

[7.22 题解](https://www.kdocs.cn/l/shvH8qsctES4?f=201)

## [7.22 E]
-----

实在是太妙了orz。。再说一次，枚举顺序要想到状压啊

``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

typedef long long ll;
const int N = 1e5 + 10;
char s[N];
ll n, pos[25][30], dp[1 << 21][30], ans;
// pos[i, j] 表示接上 i 串前 1 在位置 j，接上后的位置
// dp[s, i] 表示已接上的串的集合为 s，1 在位置 i 的方案数

int main() {
    cin >> n;
    rep(i, 1, n) {
        scanf("%s", s + 1);
        int len = strlen(s + 1);
        rep(j, 0, 26) {  // 枚举接上 i 串前的 1 在哪（ 0 表示 sum
            pos[i][j] = j;
            rep(k, 1, len) {
                if (s[k] - 'a' + 1 == pos[i][j]) pos[i][j] = 0;  // 与 sum 交换了
                else if (!pos[i][j]) pos[i][j] = s[k] - 'a' + 1;
            }
        }
    }
    dp[0][0] = 1;
    rep(s, 0, (1 << n) - 1) {
        rep(j, 0, 26) {
            if (dp[s][j]) {
                rep(i, 1, n)
                    if (!(s & (1 << (i - 1))))
                        dp[s | (1 << (i - 1))][pos[i][j]] += dp[s][j];
            }
        }
    }
    rep(i, 1, 26) ans += dp[(1 << n) - 1][i];
    printf("%lld\n", ans);
    return 0;
}
```

## [7.22 F]
-----

cf453b 的加强版

bi 大于 100 显然不优，所以考虑 100 以内的数。

100 以内质数有 25 个，不能直接状压。考虑到 50 以内的质数有 15 个，而 50 以上的质数必然只会单独出现且越小越好。选 ai 去匹配，更大一些的 ai 显然不会更差。

所以考虑将 a 从小到大排序，枚举 i，先用 50 以内的质数处理出前 i 个数的结果，再考虑贪心选取大于 50 的质数、与后 n - i 个数一一匹配的结果。

## [7.23 C]
-----

原题是 [清扫银河](http://uoj.ac/contest/51/problem/513) ！

## [7.23 D]
-----

咕咕

## [7.23 E]
-----

咕咕

## [7.23 F]
-----

咕咕

## [7.23 G]
-----

咕咕