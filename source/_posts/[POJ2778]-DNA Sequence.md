---
title: '[POJ2778]-DNA Sequence'
date: 2019-10-15 10:55:40
tags: 
    - AC自动机
    - 矩阵乘法
mathjax: true
hidden: true
---

[传送门](https://vjudge.net/problem/POJ-2778)

一开始看题的反应：噫这不是 [POJ2000]-病毒 吗（类似类似）可以 AC 自动机上 DP 的样子

后来看到了 n 的范围：这么大？那大概要矩阵加速了（然而不知道怎么写。。

DP 大概就是设 f[i, j] 表示停在自动机上的 i 点，走了 j 步的方案数

这个方程的第二维用矩阵加速省掉。

可以看 [这个dalao](https://www.cnblogs.com/LQLlulu/p/9344774.html) 写的，很详细！

code :
``` c++
#include <map>
#include <queue>
#include <cstdio>
#include <cstring>
#include <iostream>
#define rep(i, x, y) for (int i = x; i <= y; i++)
#define int long long
using namespace std;

typedef long long ll;
const int mod = 100000, N = 1100;
map<char, int> mp;
ll n, m;
int ch[N][4], idx, fail[N], val[N];
char s[15];
struct matrix { ll x[102][102]; }T, ans;

struct AC{
        void insert(char *s) {
        int u = 0, len = strlen(s + 1);
        rep(i, 1, len) {
            int c = mp[s[i]];
            if (!ch[u][c]) ch[u][c] = ++idx;
            u = ch[u][c];
        }
        val[u] = 1;
    }

    queue<int> q;
    void getfail() {
        rep(c, 0, 3)
            if (ch[0][c])
                q.push(ch[0][c]), fail[ch[0][c]] = 0;
                
        while (q.size()) {
            int u = q.front(); q.pop();
            rep(c, 0, 3) {
                if (!ch[u][c]) {
                    ch[u][c] = ch[fail[u]][c];
                    continue;
                }
                int x = ch[u][c];
                q.push(x);
                int v = fail[u];
                while (v && !ch[v][c]) v = fail[v];
                fail[x] = ch[v][c];
                val[x] |= val[fail[x]];
            }
        }
    }
}ac;

matrix operator * (matrix a, matrix b) {
    matrix c;
    rep(i, 0, idx)
        rep(j, 0, idx) {
            c.x[i][j] = 0;
            rep(k, 0, idx) {
                c.x[i][j] = (c.x[i][j] + a.x[i][k] * b.x[k][j] % mod);
                if (c.x[i][j] >= mod) c.x[i][j] -= mod;  // 不加会TLE
            }
        }
    return c;
}

matrix quick_pow(matrix a, ll b) {
    matrix ret;
    memset(ret.x, 0, sizeof(ret.x));
    rep(i, 0, idx) ret.x[i][i] = 1;
    for (; b; b >>= 1) {
        if (b & 1) ret = ret * a;
        a = a * a;
    } return ret;
}

signed main() {
    mp['A'] = 0, mp['C'] = 1, mp['T'] = 2, mp['G'] = 3;
    cin >> m >> n;
    rep(i, 1, m) {
        scanf("%s", s + 1);
        ac.insert(s);
    }
    ac.getfail();

    memset(T.x, 0, sizeof(T.x));
    rep(i, 0, idx) {
        if (val[i]) continue;
        rep(j, 0, 3)
            if (!val[ch[i][j]])
                T.x[i][ch[i][j]]++;
    }

    memset(ans.x, 0, sizeof(ans.x));
    ans.x[0][0] = 1;
    ans = ans * quick_pow(T, n);

    ll ret = 0;
    rep(i, 0, idx)
        ret = (ret + ans.x[0][i]) % mod;
    printf("%lld\n", ret);
    return 0;
}
```