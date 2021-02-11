---
title: 'AGC027E、ARC110E 等一类删除计数问题'
date: 2021-02-08 14:17:40
tags: 
    - 思维
mathjax: true
---

Atcoder 好喜欢出诸如此类的题：你有一个由 '$a$' ~ '$?$' 构成的字符串，每次可以按某种规则删去一些再加上一些，问最终能形成多少种本质不同的字符串。

偏偏是我的克星。完全没得思路啊摔。仅有的两道 套路倒是相同的 qwq

### $AGC027E$

规则：只有 $ab$，每次删 '$aa$' 为 '$b$' 或删 '$bb$' 为 '$a$'。

如果我们将 $a$ 看作 $1$，将 $b$ 看作 $2$，在模 $3$ 意义下字符串不变。令 $p(s)$ 表示 $s$ 字符之和模 $3$。

一个字符串 $s$ 能规约到字符 $a$，当且仅当 $p(s) = p(a)$ 且 $s$ 中存在两个相邻的相同字符。（归纳证明：$|s| = 1$ 或 $2$ 时显然成立，否则你可以不断操作第一对相邻的相同字符。）

$t$ 能否被 $s$ 到达？容易贪心的想到「把 $s$ 分段后让 $t$ 的每个字符与对应段匹配」，且让分的段尽量靠前。于是就可以倒着 dp：$f_i$ 表示以 $[i, |s|]$ 为后缀有多少种分段方案数。

注意：最后可能剩下一段 $abababab$ 这样无法规约的段，但是它总能被前面部分通过调整规约顺序给化为最终串中的空串。

<details>
    <summary>code</summary>
    ``` c++
    #include <bits/stdc++.h>
    #define rep(i, x, y) for (int i = x; i <= y; i++)
    using namespace std;

    typedef long long ll;
    const int mod = 1e9 + 7, N = 1e5 + 5;
    int n;
    int nxt[N][2];
    char s[N];
    ll f[N];

    ll add(ll x, ll y) { return (x + y) % mod; }
    ll sub(ll x, ll y) { return (x - y + mod) % mod; }

    int main() {
        scanf("%s", s + 1);
        n = strlen(s + 1);
        int ff = 1, sum = 0;
        rep(i, 1, n - 1) if (s[i] == s[i + 1]) { ff = 0; break; }
        if (ff) return puts("1"), 0;

        f[n + 1] = 1;
        nxt[n + 1][0] = nxt[n + 1][1] = nxt[n + 2][0] = nxt[n + 2][1] = n + 2;
        for (int i = n; i; --i) {
            (sum += s[i] == 'a' ? 1 : 2) %= 3;
            nxt[i][0] = s[i] == 'a' ? i + 1 : (s[i + 1] == 'b' ? i + 2 : nxt[i + 2][0]);  // 这一段 p = 1
            nxt[i][1] = s[i] == 'b' ? i + 1 : (s[i + 1] == 'a' ? i + 2 : nxt[i + 2][1]);  // p = 2
            f[i] = add(f[nxt[i][0]], f[nxt[i][1]] + (sum == 0));
        }
        printf("%lld\n", sub(f[1], sum == 0));
        return 0;
    }
    ```
</details>

### $ARC110E$

规则：只有 $ABC$，每次把两个相邻的不同字符替换为和它俩都不同的那个字符。

*解题过程与上面那道完全相同。*

如果我们将 $A$ 看作 $1$，$B$ 看作 $2$，$C$ 看作 $3$，在异或意义下字符串不变。令 $p(s)$ 表示 $s$ 异或和。

一个字符串 $s$ 能规约到字符 $a$，当且仅当 $p(s) = p(a)$ 且 $s$ 中存在两个相邻的不同字符。

<details>
    <summary>code</summary>
    ``` c++
    #include <bits/stdc++.h>
    #define rep(i, x, y) for (int i = x; i <= y; i++)
    #define int long long
    using namespace std;

    typedef long long ll;
    const int N = 1e6 + 10, mod = 1e9 + 7;
    int n;
    int a[N], lst[4], pre[N];
    char s[N];
    ll f[N];

    ll add(ll x, ll y) { return (x + y) % mod; }
    ll sub(ll x, ll y) { return (x - y + mod) % mod; }

    signed main() {
        cin >> n;
        scanf("%s", s + 1);
        bool ff = 1;
        rep(i, 1, n - 1) if (s[i] != s[i + 1]) { ff = 0; break; }
        rep(i, 1, n) a[i] = s[i] - 'A' + 1, pre[i] = (pre[i - 1] ^ a[i]);
        if (ff) return puts("1"), 0;
        
        for (int i = n; i >= 1; --i) {
            f[i] = (pre[n] ^ pre[i]) == 0;
            rep(j, 1, 3) {
                f[i] = add(f[i], f[lst[j ^ pre[i]]]);
            }
            lst[pre[i]] = i;
        }
        ll ans = 0;
        rep(i, 1, 3) ans = add(ans, f[lst[i]]);
        printf("%lld\n", ans);
        return 0;
    }
    ```
</details>

## 总结

变中找不变。~~（苍 白 无 力）~~

找性质题有什么总结啊【暴躁】