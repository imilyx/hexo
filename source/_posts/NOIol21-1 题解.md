---
title: 'NOIol21-1 题解'
date: 2021-04-01 18:05:00
tags: 
mathjax: true
---

今年比去年难多了！xml 没有任何长进 难过子

## $T1.$ 愤怒的小 N

第二部分没调出来，但是先把推的记录一下免得以后找不着:

结论 1: $\sum\limits_{i = 0, cnt(i) \& 1}^{2^k - 1} i^p = \sum\limits_{i = 0, cnt(i) \& 1 = 0}^{2^k - 1} i^p$

$ans = \frac{1}{2} \sum\limits_{i = 0}^{n - 1} (1 - (-1)^{cnt(i)}) f(i)$

第一部分：

$\sum\limits_{i = 0}^{n - 1} f(i) = \sum\limits_{i = 0}^{k - 1} a_i \sum\limits_{j = 0}^{n - 1} j^i$

设 $S_i(n) = \sum\limits_{j = 0}^{n - 1} j^i$

$$S_{i + 1}(n) = \sum\limits_{j = 0}^{n - 1} (j + 1)^{i + 1} - n^{i + 1}$$
$$= \sum\limits_{j = 0}^{n - 1} \sum\limits_{c = 0}^{i + 1} \binom{i + 1}{c} j^c - n^{i + 1}$$
$$= \sum\limits_{c = 0}^{i + 1} \binom{i + 1}{c} S_c(n) - n^{i + 1}$$
$$= S_{i + 1}(n) + (i + 1)S_i(n) + \sum\limits_{c = 0}^{i - 1} \binom{i + 1}{c} S_c(n) - n^{i + 1}$$

所以 $S_i(n) = \frac{ n^{i + 1} - \sum\limits_{c = 0}^{i - 1} \binom{i + 1}{c} S_c(n) }{i + 1}$ 这部分可以 $O(K^2)$ 计算。

结论 2: 形如 $\sum\limits_{i = 0}^{2^n - 1} (-1)^{cnt(i)} i^k$ 在 $n > k$ 时 $= 0$。 

证明：看作 $n$ 元集合在里面任意选，第 $i$ 个元素的权值是 $2^i$。一种物品集合 $t$ 的系数是 $\sum\limits_{s \geq 0, t \subseteq s}^{2^n - 1} (-1)^{cnt(s)}$，显然这个东西只在 $t = 2^n - 1$ 时不等于 $0$。而 $n > k$ 就意味着 $t$ 不可能为全集。

第二部分：

$$\sum\limits_{i = 0}^{n - 1} (-1)^{cnt(i)} f(i)$$
$$= \sum\limits_{i = 0}^{k - 1} a_i \sum\limits_{j = 0}^{n - 1} (-1)^{cnt(j)} j^i$$
$$= \sum\limits_{i = 0}^{k - 1} a_i \sum\limits_{j = 0, n_j = 1}^{n - 1} (-1)^{cnt(T)} \sum\limits_{l = 0}^{2^j - 1} (-1)^{cnt(l)} (T + l)^j$$

$T = \sum\limits_{o = j}^{n - 1} (str[o] - '0') * 2^{o - j + 1}$。二项式展开

$$= \sum\limits_{i = 0}^{k - 1} a_i \sum\limits_{j = 0, n_j = 1}^{n - 1} (-1)^{cnt(T)} \sum\limits_{l = 0}^{i} \binom{i}{l} T^l ( \sum\limits_{p = 0}^{2^j - 1} (-1)^{cnt(p)} p^{j - l} )$$

后面那坨设它等于 $sum(j, i - l)$

$$sum(i, j) = sum(i - 1, j) - \sum\limits_{p = 0}^j \binom{j}{p} (2^{i - 1})^p sum(i - 1, j - p)$$

根据结论 2，

$$\sum\limits_{i = 0}^{n - 1} (-1)^{cnt(i)} f(i)$$

$$= \sum\limits_{i = 0}^{k - 1} \sum\limits_{j = 0}^{\min(k, n) - 1} (-1)^{cnt(T)} \sum\limits_{l = 0}^i \binom{i}{j} T^l sum(j, i - l)$$

这部分可以 $O(k^3 + n)$ 计算。

### $T2.$ 积木小赛

SAM 的话记录每个长度区间哪些长度出现过。然而实际上 hash 就行了。SAM 是 $O(n^2)$ 的，hash 需要 map/排序，我写双模就 TLE $80$ 了 /dk

### $T3.$ 岛屿探险

算是最正常的一道题了吧。。

询问差分，离线，从前往后做。

还是拆位的思想。

使 $a ^ c \leq b$ 的 $c$ 必定是从高到低一段 $0$ 然后一个 $1$ 剩下任取。

把 $(a, b)$ 复制 $log$ 份塞进对应的所有 $c$ 的合法区间，现在要用 $(c, d)$ 去切这 $log$ 个区间，那么把 $(c, d)$ 也复制 $log$ 份塞进所有包含 $c$ 的区间即可。

$log$ 个位置分别做，开 $01 trie$ 查询 $a ^ c \leq d$ 的 $a$ 的个数。

<details>
    <summary>code</summary>
    ``` c++
    #include <bits/stdc++.h>
    #define rep(i, x, y) for (int i = x; i <= y; i++)
    #define pb push_back
    #define mkp make_pair
    #define fi first
    #define se second
    using namespace std;

    const int N = 25e5 + 3, M = (1 << 25) + 3, E = 75e5 + 3, O = 1e5 + 3;
    int n, Q, id, a[O], b[O], c[O], d[O], ans[O];
    int lnk[M], cnt, to[E], nxt[E];
    vector<int> q[O];

    void rd(int &x) {
        x = 0; char ch = getchar();
        for (; !isdigit(ch); ch = getchar());
        for (; isdigit(ch); ch = getchar()) x = (x << 1) + (x << 3) + ch - '0';
    }

    int iabs(int x) { return x < 0 ? -x : x; }

    inline void adde(int x, int y) {
        to[++cnt] = y, nxt[cnt] = lnk[x], lnk[x] = cnt;
    }

    inline void insert(int x, int l, int r, int pos, int id) {
        adde(x, id);
        if (l == r) return;
        int mid = (l + r) >> 1;
        pos <= mid ? insert(x << 1, l, mid, pos, id) : insert(x << 1 | 1, mid + 1, r, pos, id);
    }

    struct Trie {
        int ch[N][2], idx, sz[N];
        inline void init() {
            idx = 1;
            ch[1][0] = ch[1][1] = sz[1] = 0;
        }
        inline void ins(int val) {
            int u = 1;
            for (int i = (1 << 23); i; i >>= 1) {
                int c = (val & i) > 0;
                if (!ch[u][c]) ch[u][c] = ++idx, ch[idx][0] = ch[idx][1] = sz[idx] = 0;
                u = ch[u][c];
                sz[u]++;
            }
        }
        inline int qry(int c, int d) {
            int u = 1, ret = 0;
            for (int i = (1 << 23); i; i >>= 1) {
                if (d & i) {
                    int op = (c & i) > 0;
                    ret += sz[ch[u][op]], u = ch[u][!op];
                } else {
                    u = ch[u][(c & i) > 0];
                }
            } return ret + sz[u];
        }
    } tr;

    int main() {
        rd(n), rd(Q);
        rep(i, 1, n) {
            rd(a[i]), rd(b[i]);
        }
        int l, r;
        rep(i, 1, Q) {
            rd(l), rd(r), rd(c[i]), rd(d[i]);
            q[l - 1].pb(-i), q[r].pb(i);
        }
        for (int i = n; i; --i) {
            for (int j = 0; j < q[i].size(); j++) {
                insert(1, 0, (1 << 24) - 1, c[iabs(q[i][j])], q[i][j]);
            }
            int x = 1;
            for (int j = (1 << 23); j; j >>= 1) {
                x *= 2;
                if (a[i] & j) x ^= 1;
                if (b[i] & j) adde(x, i + Q), x ^= 1;
            }
            adde(x, i + Q);
        }
        for (int i = (1 << 25) - 1; i >= 0; i--) {
            if (!lnk[i]) continue;
            tr.init();
            for (int j = lnk[i]; j; j = nxt[j]) {
                int x = to[j], xx = iabs(x);
                if (xx <= Q)
                    ans[xx] += (x > 0 ? 1 : -1) * tr.qry(c[xx], d[xx]);
                else
                    tr.ins(a[x - Q]);
            }
        }
        rep(i, 1, Q) printf("%d\n", ans[i]);
        return 0;
    }
    ```
</details>