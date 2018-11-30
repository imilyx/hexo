---
title: '[SCOI2009]-windy数'
date: 2018-11-30 22:50:00
tags:
	- 数位DP
---

哦哦哦第一次做数位DP！新奇的体验！！然后做题技巧一般是先预处理DP再统计答案。统计答案时，个人感觉就是先大量快速处理掉跟位数没关系的再分情况处理跟位数有关系的。应该重难点是统计答案吧。

本题中我们设 dp[i, j] 表示 i 位且最高位为 j 的windy数的个数。预处理转移方程比较简单：dp[i, j] = sum{dp[i - 1, k]} |j - k| >= 2. 其中不要忘记边界：dp[1, j] = 1 (0 <= j <= 9)

统计答案也很好玩：

solve(x) 为 x 以内(不包括 x) 的windy数个数。设 len 为 x 的位数。
1. 统计长度为 len 且最高位 < x 最高位的windy数个数
2. 统计长度 < len 的windy数个数
3. 统计长度为 len 且最高位 = x 最高位的windy数个数（依次枚举每一位能否取小）

所以最后就是输出 solve(b + 1) - solve(a) (b => b + 1, a - 1 => a!)

``` c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
ll dp[20][20], a, b, digit[20];

void prework() {
    for (int i = 0; i <= 9; i++) dp[1][i] = 1;
    for (int i = 2; i <= 10; i++)
        for (int j = 0; j <= 9; j++)
            for (int k = 0; k <= 9; k++)
                if (fabs(j - k) >= 2)  // 至少为2！
                    dp[i][j] += dp[i - 1][k];
}

ll solve(ll x) {
    ll ans = 0;
    if (x == 0) return 0;
    memset(digit, 0, sizeof(digit));
    int len = 0;
    while (x > 0) {
        digit[++len] = x % 10; x /= 10;
    }
    for (int i = 1; i < digit[len]; i++) ans += dp[len][i];
    for (int i = len - 1; i > 0; i--)
        for (int j = 1; j <= 9; j++)
            ans += dp[i][j];
    for (int i = len - 1; i > 0; i--) {
        for (int j = 0; j < digit[i]; j++)
            if (fabs(digit[i + 1] - j) >= 2) ans += dp[i][j];
        if (abs(digit[i + 1] - digit[i]) < 2) break; // 当目前这一步不满足时，再下去都是不满足的！
    }
    return ans;
}

int main() {
    prework();
    scanf("%lld%lld", &a, &b);
    printf("%lld\n", solve(b + 1) - solve(a));
    return 0;
}
```