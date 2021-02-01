---
title: '[LeetCode466]-Count The Repetitions'
date: 2018-09-16 22:56:40
tags: 
    - 倍增DP
mathjax: true
hidden: true
---

LeetCode 提交方式真的十分清奇古怪哇。。。

题意：定义 conn(s, n) 为 n 个字符串 s 首尾相接形成的字符串，例如，conn("abc", 2) = "abcabc".

我们称字符串 "abdbec" 能生成 "abc"，而 "acbbe" 不能生成 "abc". 一句话，若 a 能生成 b，则 b 是 a 的一个不连续字串。给定两个字符串 s1, s2, 以及两个整数 n1，n2，求最大的 m，满足 conn(conn(s2, n2), m) 能被 conn(s1, n1) 生成。|s1|、|s2| <= 100, n1、n2 <= 1e6.

因为 $conn(conn(s2, n2), m) = conn(s2, n2 * m)$, 所以我们要求出一个最大的 $m^{'}$, 满足 $conn(s2, m^{'})$ 能被 $conn(s1, n1)$ 生成，最后答案就是 $m^{'} / n2$.

$m^{'}$ 的上界为 $|s1| * n1 / |s2|$. 然后，我们的倍增上场啦～ $m^{'}$ 可以表示成 $m^{'} = 2^{p_{t - 1}} + 2^{p_{t - 2}} + \cdots + 2^{p_1} + 2^{p_0}$，同样可以把 $conn(s2, m^{'})$ 看作许多 2 的幂次方倍的字符串相接而成。

设 f[i, j] 表示从 s1[i] 开始，至少需要多少个字符，才能生成 $conn(s2, 2^j)$. 状态转移方程为：

$$f[i, j] = f[i, j - 1] + f[(i + f[i, j - 1])\ mod\ |s1|, j - 1]$$

初始：因为 |s1| 和 |s2| 非常小，f[i, 0] 可以朴素计算。

最后对DP得到的状态进行拼接。

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
ll f[105][32];
string s1, s2;
int n1, n2;

int main() {
    cin >> s1 >> n1 >> s2 >> n2;
    int len1 = s1.length(), len2 = s2.length();
    for (int i = 0; i < len1; i++) {
        int pos = i;
        f[i][0] = 0;
        for (int j = 0; j < len2; j++) {
            int cnt = 0;
            while (s1[pos] != s2[j]) {
                pos = (pos + 1) % len1;
                if (++cnt >= len1) {
                    cout << 0 << endl;
                    return 0;
                }
            }
            pos = (pos + 1) % len1;
            f[i][0] += cnt + 1;
        }
    }
    for (int j = 1; j <= 30; j++)
        for (int i = 0; i < len1; i++)
            f[i][j] = f[i][j - 1] + f[(i + f[i][j - 1]) % len1][j - 1];
    ll m = 0;
    for (int st = 0; st < len1; st++) {
        ll x = st, ans = 0;
        for (int j = 30; j >= 0; j--)
            if (x + f[x % len1][j] <= len1 * n1) {
                x += f[x % len1][j];
                ans += 1 << j;
            }
        m = max(m, ans);
    }
    cout << m / n2 << endl;
    return 0;
}
```

这里先挂一个main()函数里的代码吧～