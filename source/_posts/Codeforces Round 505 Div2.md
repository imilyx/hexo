---
title: 'Codeforces Round 505 Div2'
date: 2018-08-20 17:36:40
tags: 
    - 比赛
mathjax: true
---

[比赛链接](http://codeforces.com/contest/1025)

在比赛前睡了一会，果然精神不少！据说这是场数学大赛？比较考思维！B题差一点做出来啦！specialist后的第1场比赛！

### A. Doggo Recoloring
-----

题意：有一个字符串，包含小写英文字母。每次你可以选择一种个数大于1的字母将它们统统变为另一种字母，求能否统一字符串。

只要有一种个数大于1的字母就行了，可以26种字母统计数量后排序判断。

### B. Weakened Common Divisor
-----

题意：有 n 对数字，要求求出一个WCD，要大于1，而且每对数字里有至少一个是它的倍数。

一开始想了一个质因数分解，是将每对数字都分解一下，T掉了。后来知道如果根据第一对数字的质因数分解应该也是可以的。

第二次想了**接近**正解的方法。就是求出每队数字lcm的gcd，然后找出这个gcd的最小质因子。一提交就发现错了，自己都能造出数据来hack。。（后来果然被hack啦）—— gcd是 $10^{18}$ 级别的，质因子最大是 $10^9$ 的，怎么找？

后来发现自己傻了。。。gcd是 $10^{18}$ 的，但每对数字是 $10^9$ 的呀，而那个最小质因子必定是第一对数字其中之一的约数，考虑第一对数字就好了。

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
int n;
ll a[150010], b[150010], sum;

int main() {
    scanf("%d", &n);
    scanf("%lld%lld", &a[1], &b[1]);
    sum = a[1] / (__gcd(a[1], b[1])) * b[1];
    for (int i = 2; i <= n; i++) {
        scanf("%lld%lld", &a[i], &b[i]);
        sum = __gcd(sum, a[i] / (__gcd(a[i], b[i])) * b[i]);
    }
    if (sum == 1) {
        printf("-1\n"); return 0;
    }
    ll ans = 0;
    for (int i = 2; i * i <= a[1]; i++) {
        if (!ans && sum % i == 0) {
            ans = i; break;
        }
        while (a[1] % i == 0) a[1] /= i;
    }
    if (!ans && a[1] > 1 && sum % a[1] == 0) ans = a[1];
    for (int i = 2; i * i <= b[1]; i++) {
        if (!ans && sum % i == 0) {
            ans = i; break;
        }
        while (b[1] % i == 0) b[1] /= i;
    }
    if (!ans && b[1] > 1 && sum % b[1] == 0) ans = b[1];
    printf("%lld\n", ans);
    return 0;
}
```


### C. Plasticine zebra
-----

题意：有一个由 'b' 'w' 两种字母组成的字符串，每次你可以选择一个位置 x，翻转 1 ~ x 和 x + 1 ~ n, 求能达到的最长交错字串（即一个 b 一个 w）长度。

一个长度为 8 的字符串，位置分别为 12345678，可以发现如果在第 4 个位置翻转，变成了 43218765，在第 5 个位置翻转变成了 81234567，有没有发现，这是一个 12345678 的变形，即把 8 移到首位？

同样第一次翻转时我们也可以将 43218765 倒过来，看作 56781234， 又是 12345678 的变形。

这样我们可以将字符串复制一份接在末尾，然后寻找最长交错字串。不要忘记与 n 取 max。

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

char s[200010];

int main() {
    scanf("%s", s + 1);
    int n = strlen(s + 1), tmp = 0, ans = 0;
    for (int i = 1; i <= n; i++) s[i + n] = s[i];
    for (int i = 1; i <= 2 * n - 1; i++)
        if (s[i] != s[i + 1]) tmp++;
        else ans = max(ans, tmp), tmp = 1;
    ans = max(ans, tmp);
    printf("%d\n", min(ans, n));
    return 0;
}
```

### D. Recovering BST
-----

题意：求用给定的数字，能否做成一棵“有边相邻的两个节点都不互质”的二叉搜索树。

可以发现，若 i 号是根，根的左子树就是 1 ~ i - 1 号，右子树就是 i + 1 ~ n 号。所以说，二叉搜索树中的子树是可以和区间划上等号的。

我们设 f[l, r, 0] 表示 l ~ r 这个区间是 l - 1 号的右子树，f[l, r, 1] 表示 l ~ r 这个区间是 r + 1 号的左子树。f[l, r, 0/1] 的取值为 0 或 1，分别表示 不存在/存在。

需要注意的是，我第一次T了，但 n <= 700, $n^3$ 没问题的啊。那么就是常数大了，需要卡常。

我们知道，c++ \<algorithm> 库中的 __gcd() 函数是 log 的，$n^3\ log\ n$ 可能太大了，我们将每一对点的gcd先预处理出来，$n^2\ log\ n$ 就可以过了，美滋滋！

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

int n, a[710], f[710][710][2], g[710][710];

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) {
        scanf("%d", &a[i]);
    }
    a[0] = a[n + 1] = 1;
    for (int i = 0; i <= n + 1; i++)
        for (int j = i; j <= n + 1; j++)
            g[i][j] = g[j][i] = (__gcd(a[i], a[j]) > 1);
    for (int i = 1; i <= n + 1; i++) {
        f[i][i][0] = g[i][i - 1];
        f[i][i][1] = g[i][i + 1];
        f[i][i - 1][1] = f[i + 1][i][0] = 1;
    }
    for (int len = 1; len <= n; len++)
        for (int l = 1; l <= n - len + 1; l++) {
            int r = l + len - 1;
            for (int k = l; k <= r; k++) {
                if (g[k][l - 1] && f[l][k - 1][1] && f[k + 1][r][0])
                    f[l][r][0] = 1;
                if (g[k][r + 1] && f[l][k - 1][1] && f[k + 1][r][0])
                    f[l][r][1] = 1;
            }
        }
    for (int i = 1; i <= n; i++)
        if (f[1][i - 1][1] && f[i + 1][n][0]) {
            printf("Yes\n"); return 0;
        }
    printf("No\n");
    return 0;
}
```