---
title: '20Feb CF&AT 练题汇总'
date: 2020-02-29 23:59:59
tags: 
    - CF&AT往届
mathjax: true
---

## [AT1984]-Wide Swap
-----

[见此篇](https://imilyx.github.io/2020/02/09/%5BAT1984%5D-Wide%20Swap/)

## [AT2004]-Anticube
-----

[见此篇](https://imilyx.github.io/2020/02/10/%5BAT2004%5D-Anticube/)

## [CF17C]-Balance
-----

* 找性质：定义一个字符串的最简化为删除相邻同类字符后的字符串，例如：aabbbc 最简化为 abc。变化后的 B 的最简化是变化前 A 的最简化的子序列。不难懂，但是很难想。。。

我觉得这个性质应该切入点是：字符串 S 中 l 的位置要同化 [l, r] 这个区间，显然是像推土机一样推过去的，它以自己替换了 [l, r] 的最简化。

简单 DP 即可。

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int mod = 51123987, N = 160;
int n, nxt[N][3], f[N][55][55][55], ans;
char s[N];

void upd(int &x, int y) { x = (x + y) % mod; }

int main() {
    cin >> n;
    scanf("%s", s + 1);

    rep(i, 0, 2) nxt[n + 1][i] = -1;
    for (int i = n; i >= 1; i--) {
        rep(j, 0, 2) nxt[i][j] = nxt[i + 1][j];
        nxt[i][s[i] - 'a'] = i;
    }

    f[1][0][0][0] = 1;
    int lim = (n + 2) / 3;
    rep(i, 1, n)
        rep(a, 0, lim)
            rep(b, 0, lim)
                rep(c, 0, lim) {
                    if (a + b + c == n) {
                        if (abs(a - b) <= 1 && abs(a - c) <= 1 && abs(b - c) <= 1)
                            upd(ans, f[i][a][b][c]);
                        continue;
                    }
                    if (nxt[i][0] != -1)  // 用 nxt 来 和用 s 最简化后的字符串来是一个道理
                        upd(f[nxt[i][0]][a + 1][b][c], f[i][a][b][c]);  // 有可能会无限停留在当前位置，不过没有关系，a + b + c = n 的时候就会停止
                    if (nxt[i][1] != -1)
                        upd(f[nxt[i][1]][a][b + 1][c], f[i][a][b][c]);
                    if (nxt[i][2] != -1)
                        upd(f[nxt[i][2]][a][b][c + 1], f[i][a][b][c]);
                }
    printf("%d\n", ans);
    return 0;
}
```

## [CF17D]-Notepad
-----

没有什么好说的，一眼不是高精度！。没错就是--

* 扩展欧拉函数板子题！【啊怎么又是欧拉

[这个大佬精辟总结了](https://www.cnblogs.com/ywwyww/p/8510981.html)

题目没有保证底数(b)和模数(c)互质所以不能用数论中的欧拉定理，要用扩展版本。**还有一点，注意指数小于 phi(模数) 的时候定理不成立！**

``` c++
// 扩展欧拉函数 降幂
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 1e6 + 10;
typedef long long ll;
ll b, b2, n, c, phi;
char s1[N], s2[N];

ll getphi(ll n) {
    ll ans = n;
    for (int i = 2; i * i <= n; i++) {
        if (n % i == 0) ans = ans / i * (i - 1);
        while (n % i == 0) n /= i;
    }
    if (n > 1) ans = ans / n * (n - 1);
    return ans;
}

ll quick_pow(ll a, ll b) {
    ll ret = 1;
    for (; b; b >>= 1) {
        if (b & 1) ret = ret * a % c;
        a = a * a % c;
    } return ret;
}

int main() {
    scanf("%s%s%lld", s1 + 1, s2 + 1, &c);
    phi = getphi(c);
    
    int len = strlen(s1 + 1);

    // b
    rep(i, 1, len) b = (b * 10 + (s1[i] - '0')) % c;
    // b - 1
    b2 = (b - 1ll + c) % c;

    len = strlen(s2 + 1);
    rep(i, 1, len) n = (n * 10 + (s2[i] - '0')) % phi;
    // n - 1
    n = (n - 1ll + phi) % phi + phi;

    if (len < 10) {
        ll realN = 0;
        rep(i, 1, len) realN = realN * 10 + (s2[i] - '0');
        realN -= 1ll;
        if (realN < phi) n -= phi;  // !!!
    }
    ll ans = b2 * quick_pow(b, n) % c;
    printf("%lld\n", ans ? ans : c);
    return 0;
}
```

## [CF17E]-Palisection
-----

有点意思啊！

题目要求相交回文串对数，考虑到存在包含关系，直接处理细节会很多很不好写。

俗话说得好，**正难则反！**

* 相交对数 = 总数 - 不相交对数。manacher + 前缀和维护

计算一个位置左边的回文串右端点个数，与该位置右边的回文串左端点个数相乘，这样是会**算重**的！！

所以我们固定一个位置，该位置作为左端点的串个数 与该位置左边的回文串右端点相乘，这样显然不会重。

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 8e6 + 10, mod = 51123987;
typedef long long ll;
int n, len, p[N], pos, mx, l[N], r[N];  // l[x]/r[x]: x 位置作为左/右端点的字符串个数
ll ans, sum;
char a[N], s[N];

void manacher() {
    rep(i, 1, n) {
        if (i < mx)
            p[i] = min(p[pos * 2 - i], mx - i);
        else p[i] = 1;
        while (s[i + p[i]] == s[i - p[i]]) p[i]++;
        if (mx < i + p[i]) mx = i + p[i], pos = i;

        ++l[i - p[i] + 1], --l[i + 1];  // 中心到每一个 [左端点，中心] 的点 都有一个回文串
        ++r[i], --r[i + p[i]];

        ans = (ans + (p[i] >> 1)) % mod;
    }
    ans = (ans * (ans - 1) / 2) % mod;
}

int main() {
    scanf("%d%s", &n, a + 1);
    len = n;
    s[n = 1] = '#';
    rep(i, 1, len) s[++n] = a[i], s[++n] = '#';
    s[0] = '[', s[n + 1] = ']';

    manacher();

    rep(i, 1, n) {
        l[i] += l[i - 1], r[i] += r[i - 1];
        if (i % 2 == 0) {
            ans = (ans - (ll)l[i] * sum % mod + mod) % mod;  // 正难则反
            sum = (sum + r[i]) % mod;  // 相当于二阶差分
        }
    }
    printf("%lld\n", ans);
    return 0;
}
```

## [AT2301]-Solitaire
-----

取数前肯定是 V 字形的（1 是谷底）

* 一个取数序列，只要存在一种 V 字形的安排，就是合法的

[这个大佬讲的比我清楚](https://blog.csdn.net/weixin_30820077/article/details/96464019)

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
#define per(i, x, y) for (int i = x; i >= y; i--)
using namespace std;

const int mod = 1e9 + 7, N = 2e3 + 10;
typedef long long ll;
int n, K, f[N][N], sum, ans;

int main() {
    cin >> n >> K;
    per(i, n, 2) f[1][i] = 1;
    rep(i, 1, K - 2) {
        sum = f[i][n - i + 1];
        per(j, n - i, 2) {
            sum = (sum + f[i][j]) % mod;
            f[i + 1][j] = (f[i + 1][j] + sum) % mod;
        }
    }
    rep(i, 2, n - (K - 1) + 1) ans = (ans + f[K - 1][i]) % mod;
    if (K == 1) ans = 1;
    if (n - K - 1 > 0)
        rep(i, 1, n - K - 1) ans = (ans << 1) % mod;
        
    printf("%d\n", ans);
    return 0;
}
```

## [AT2005]-Sequential operations on Sequence
-----

[见此篇](https://imilyx.github.io/2020/02/11/[AT2005]-Sequential%20operations%20on%20Sequence/)

## [AT2043]-AND Grid
-----

构造题，以前做到过！很神仙的思路，想不到qwq 构造形状如嵌在一起的手指一般

## [AT2046]-Namori
-----

简介（雾）：一道绝妙的转化好题

**树 是 二 分 图！！！**

[这个大佬就写的到位，偶环部分写得特别好](https://www.luogu.com.cn/blog/flashblog/solution-at2046)

然后来谈谈自己的理解：

1. 这种转化下，将相邻两个同色同时反转 等价于 将相邻两个是否有硬币情况交换一下

2. 也许看到这种反转题第一步就应该按照深度什么的来个黑白反转。。？这样才能进展qwq。。**模型转化靠做题练**

3. 奇环和偶环要分开考虑（偶环是二分图，奇环不是）**奇环中断掉的边只有分摊部分转移、加快运输的作用**

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 2e5 + 10;
int n, m, ans, stx, sty, fx, fy;
int to[N << 1], nxt[N << 1], lnk[N], cnt;
int dep[N], fa[N], s[N], a[N], top;

void add(int x, int y) {
    to[++cnt] = y, nxt[cnt] = lnk[x], lnk[x] = cnt;
}

int getfa(int x) {
    return fa[x] == x ? x : fa[x] = getfa(fa[x]);
}

void dfs(int x, int f, int val) {
    dep[x] = dep[f] + 1;
    fa[x] = f;
    s[x] = val;
    for (int i = lnk[x], y; i; i = nxt[i])
        if ((y = to[i]) != f)
            dfs(y, x, -val), s[x] += s[y];
}

int main() {
    cin >> n >> m;
    rep(i, 1, n) fa[i] = i;
    stx = sty = 1;
    rep(i, 1, m) {
        int x, y; scanf("%d%d", &x, &y);
        if ((fx = getfa(x)) != (fy = getfa(y))) fa[fx] = fy;
        else { stx = x, sty = y; continue; }  // 环上边，给它断掉！
        add(x, y), add(y, x);
    }
    dfs(stx, 0, 1);
    if (n == m) {
        for (int i = sty; i; i = fa[i]) a[++top] = s[i];
        if ((dep[stx] - dep[sty]) & 1) {  // 偶环
            if (s[stx]) return puts("-1"), 0;
            sort(a + 1, a + top + 1);
            for (int i = sty; i; i = fa[i]) s[i] -= a[top >> 1];
        } else {  // 奇环
            if (s[stx] & 1) return puts("-1"), 0;
            for (int i = sty; i; i = fa[i]) s[i] -= s[stx] >> 1;  // 环上的树们只管把多余的往外输送就好了，主要是环上边决定运输方向！
        }
    } else if (s[stx]) return puts("-1"), 0;
    rep(i, 1, n) ans += abs(s[i]);
    printf("%d\n", ans);
    return 0;
}
```

## [AT2063]-Sugigma: The Showdown
-----

我哥解说太赞了！！！超好评

在一棵树上，先手不被抓到的唯一情况是：红树上存在一条边 x->y, 蓝树上 x 到 y 的距离 > 2。

接下来说明为什么除此之外都会被抓到。

首先，在一棵**普通**树上，后手可以把先手往角落里逼（因为没有环），先手挂了。

回到这题，“蓝树上 x 到 y 的距离 <= 2”，首先不用考虑距离 = 1 的情况（这就是普通树的情况）

现在距离 = 2，后手站在两条蓝边的中间节点上，先手在 x 或 y 上，显然如瓮中捉鳖，先手挂了。

因此先手要逃离这个结构，但一逃离就是距离 = 1 的情况，还是会挂。。

我哥讲的真好，就这个道理！

所以时间就是两倍的后手步数，先手只要找到离后手最远且他能比后手抢先到达的点呆着就好。。。

## [AT2064]-Many Easy Problems
-----

一道非模板的 NTT~ 可以想到算每个点在多少联通块里出现，但这显然不好算，正难则反，继而转为算每个点在多少联通块里不出现

[挂一个大佬博客，推的式子就是这个亚子](https://m-sea-blog.com/archives/4576/)

然后感触比较深的就是：设 F = f ✖️ g， {\sum_{i, 0, n} f(n - i)·g(i)} = F(n) ！

code :
``` c++
/*
感慨：卷积原来是这么用的啊..
*/
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int mod = 924844033, N = 4e5 + 10;
typedef long long ll;
int n, lim = 1, l;
int to[N << 1], nxt[N << 1], lnk[N], ecnt;
int sz[N], cnt[N], r[N << 1];
ll f[N], g[N], fac[N], inv[N];

void add(int x, int y) {
    to[++ecnt] = y, nxt[ecnt] = lnk[x], lnk[x] = ecnt;
}

ll quick_pow(ll a, ll b) {
    ll ret = 1;
    for (; b; b >>= 1) {
        if (b & 1) ret = ret * a % mod;
        a = a * a % mod;
    } return ret;
}

ll C(ll n, ll m) {
    return 1ll * fac[n] * inv[m] % mod * inv[n - m] % mod;
}

void pre() {
    fac[0] = inv[0] = 1;
    rep(i, 1, n) fac[i] = fac[i - 1] * i % mod;
    inv[n] = quick_pow(fac[n], mod - 2);
    for (int i = n; i >= 1; i--) inv[i - 1] = inv[i] * i % mod;
}

void dfs(int x, int fa) {
    sz[x] = 1;
    for (int i = lnk[x], y; i; i = nxt[i])
        if ((y = to[i]) != fa)
            dfs(y, x), sz[x] += sz[y], cnt[sz[y]]++;
    cnt[n - sz[x]]++;
}

void NTT(ll *a, int op) {
    rep(i, 0, lim - 1) if (i < r[i]) swap(a[i], a[r[i]]);
    for (int mid = 1; mid < lim; mid <<= 1) {
        ll W = quick_pow(op == 1 ? 5 : 554906420, (mod - 1) / (mid << 1));
        for (int j = 0; j < lim; j += (mid << 1)) {
            ll w = 1;
            for (int k = 0; k < mid; k++, w = w * W % mod) {
                ll x = a[j + k], y = w * a[j + k + mid] % mod;
                a[j + k] = (x + y) % mod, a[j + k + mid] = (x - y + mod) % mod;
            }
        }
    }
    if (op == -1) {
        ll x = quick_pow(lim, mod - 2);
        rep(i, 0, lim - 1) a[i] = a[i] * x % mod;
    }
}

int main() {
    cin >> n;
    pre();
    rep(i, 1, n - 1) {
        int x, y; scanf("%d%d", &x, &y);
        add(x, y), add(y, x);
    }
    dfs(1, 0);
    rep(i, 1, n) f[i] = 1ll * cnt[i] * fac[i] % mod;
    reverse(f, f + n + 1);
    rep(i, 0, n) g[i] = inv[i];
    while (lim <= n * 2) lim <<= 1, l++;
    rep(i, 0, lim - 1) r[i] = (r[i >> 1] >> 1) | ((i & 1) << (l - 1));
    NTT(f, 1), NTT(g, 1);
    rep(i, 0, lim) f[i] = (f[i] * g[i]) % mod;
    NTT(f, -1);
    rep(i, 1, n) printf("%lld\n", (n * C(n, i) % mod - inv[i] * f[n - i] % mod + mod) % mod);
    return 0;
}
```

