---
title: 【学习笔记】插头dp
date: 2020-07-29 17:50:40
tags: 
    - 插头dp
mathjax: true
---

[这个讲的还不错](https://www.cnblogs.com/zinthos/p/3897854.html)

重修插头dp！今日主题：讲讲那些奇怪的插头们

* 划分阶段（从上到下、从左到右逐格递推）：qvq，轮廓线以上就是已经处理好的状态，只等着根据当前格的插头状态往下推了。
* 根据当前格有无左、上插头，推向有无右、下插头的状态。
* 关于换行，我们编号 m + 1 个位置 0 ~ m, 显然当前行 0 ~ m - 1 的会转移到 1 ~ m，右移一位就好了。
* 朴素的轮廓线只有 0/1 表示有无插头，但以洛谷那道例题为例，这样处理是会出现多个回路的情况的。这时候需要用括号表示法 + X进制（X > 2, 通常取X = 4为宜因为位运算很快且写起来方便）来维护插头之间的联通性（限制了一些非法状态的转移），当然，分类情况也多了不少。。。
* Hash表可以通过去重以及排除无用状态极大的加速插头dp的速度。
* 理解不了的话，建议画图 qvq！

## [hdu1693](http://acm.hdu.edu.cn/showproblem.php?pid=1693)
-----

可以有多个回路。朴素轮廓线，可以开 f[i, j, s]，也可以滚动数组（细节注意！！！）

``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

typedef long long ll;
ll T, n, m, cas;
ll mp[15][15], f[2][1 << 13];

int main() {
    cin >> T;
    while (T--) {
        cin >> n >> m;
        memset(f, 0, sizeof(f));
        rep(i, 1, n)
            rep(j, 1, m)
                scanf("%d", &mp[i][j]);
        int cur = 1, pre = 0, tot = (1 << (m + 1)) - 1;
        f[1][0] = 1;
        rep(i, 1, n) {
            swap(cur, pre);  // !!!
            memset(f[cur], 0, sizeof(f[cur]));
            for (int s = (1 << m) - 1; s >= 0; --s)
                f[cur][s << 1] = f[pre][s];
            rep(j, 1, m) {
                swap(pre, cur);
                memset(f[cur], 0, sizeof(f[cur]));
                rep(s, 0, tot) {
                    bool is_r = (s >> (j - 1)) & 1, is_d = (s >> j) & 1;
                    if (!mp[i][j]) {
                        if (!is_r && !is_d)
                            f[cur][s] = f[pre][s];
                    } else {
                        if (is_r && is_d) {
                            f[cur][s] = f[pre][s - (1 << (j - 1)) - (1 << j)];
                        } else if (is_r ^ is_d) {
                            int ss = s | (1 << j) | (1 << (j - 1));
                            f[cur][s] = f[pre][ss - (1 << (j - 1))] + f[pre][ss - (1 << j)];
                        } else {
                            f[cur][s] = f[pre][s + (1 << (j - 1)) + (1 << j)];
                        }
                    }
                }
            }
        }
        printf("Case %d: There are %lld ways to eat the trees.\n", ++cas, f[cur][0]);
    }
    return 0;
}
```

## [洛谷模板](https://www.luogu.com.cn/problem/P5056)
-----

只能有一个闭合回路。

（一些废话：这是我上来对着题解写的第一道插头dp，hash优化 + 括号表示一个没少。。qwq 被毒死了呀

``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

typedef long long ll;
const int mod = 590027, N = 6e5 + 10;
ll n, m, ans, ex, ey, pre, cur = 1;
ll bits[30], tots[2], lnk[N], nxt[N], to[N], cnt, sta[2][N], dp[2][N];
bool mp[15][15];
char s[15][15];

void hsh(ll state, ll val) {
    ll x = state % mod;
    for (ll i = lnk[x]; i; i = nxt[i])
        if (sta[cur][to[i]] == state) {
            dp[cur][to[i]] += val; return;
        }
    tots[cur]++;
    sta[cur][tots[cur]] = state;
    dp[cur][tots[cur]] = val;

    to[++cnt] = tots[cur], nxt[cnt] = lnk[x], lnk[x] = cnt;
}

void DP() {
    ll cursta, curans;
    int is_d, is_r;
    dp[cur][tots[cur] = 1] = 1, sta[cur][1] = 0;
    rep(i, 1, n) {
        rep(j, 1, tots[cur]) sta[cur][j] <<= 2;
        rep(j, 1, m) {
            swap(pre, cur);
            memset(lnk, 0, sizeof(lnk));
            tots[cur] = cnt = 0;
            rep(k, 1, tots[pre]) {
                cursta = sta[pre][k], curans = dp[pre][k];
                is_r = (cursta >> bits[j - 1]) % 4, is_d = (cursta >> bits[j]) % 4;

                if (!mp[i][j]) {
                    if (!is_r && !is_d)
                        hsh(cursta, curans);
                }
                else if (!is_r && !is_d) {
                    if (mp[i + 1][j] && mp[i][j + 1])
                        hsh(cursta + (1 << bits[j - 1]) + 2 * (1 << bits[j]), curans);
                }
                else if (is_r && !is_d) {
                    if (mp[i][j + 1])
                        hsh(cursta - is_r * (1 << bits[j - 1]) + is_r * (1 << bits[j]), curans);
                    if (mp[i + 1][j])
                        hsh(cursta, curans);
                }
                else if (!is_r && is_d) {
                    if (mp[i][j + 1])
                        hsh(cursta, curans);
                    if (mp[i + 1][j])
                        hsh(cursta + is_d * (1 << bits[j - 1]) - is_d * (1 << bits[j]), curans);
                }
                else if (is_r == 1 && is_d == 1) {
                    int cnt = 1;
                    rep(l, j + 1, m) {
                        int t = (cursta >> bits[l]) % 4;
                        if (t == 1) ++cnt;
                        if (t == 2) --cnt;
                        if (!cnt) {
                            hsh(cursta - (1 << bits[j - 1]) - (1 << bits[j]) - (1 << bits[l]), curans);
                            break;
                        }
                    }
                }
                else if (is_r == 2 && is_d == 2) {
                    int cnt = -1;
                    for (int l = j - 2; l; --l) {
                        int t = (cursta >> bits[l]) % 4;
                        if (t == 1) ++cnt;
                        if (t == 2) --cnt;
                        if (!cnt) {
                            hsh(cursta - 2 * (1 << bits[j - 1]) - 2 * (1 << bits[j]) + (1 << bits[l]), curans);
                            break;
                        }
                    }
                }
                else if (is_r == 2 && is_d == 1) {
                    hsh(cursta - 2 * (1 << bits[j - 1]) - (1 << bits[j]), curans);
                }
                else if (is_r == 1 && is_d == 2) {
                    if (i == ex && j == ey)
                        ans += curans;
                }
            }
        }
    }
}

int main() {
    cin >> n >> m;
    rep(i, 1, n) {
        scanf("%s", s[i] + 1);
        rep(j, 1, m)
            if (s[i][j] == '.') ex = i, ey = j, mp[i][j] = 1;
            else mp[i][j] = 0;
    }
    rep(i, 1, 25) bits[i] = (i << 1);
    DP();
    printf("%lld\n", ans);
    return 0;
}
```

## [51nod1518-稳定多米诺覆盖](https://vjudge.net/problem/51Nod-1518)
-----

终于到你了，感动中国！

[链接](https://imilyx.github.io/2020/07/30/[51nod1518]-%E7%A8%B3%E5%AE%9A%E5%A4%9A%E7%B1%B3%E8%AF%BA%E8%A6%86%E7%9B%96/)

暂时就更这么多吧，后续还会做题 & 写单篇博（可能