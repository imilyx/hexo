---
title: '21Feb 学习计划'
date: 2021-02-28 23:59:59
tags: 
mathjax: true
---

## $\mathcal{2.1}$
---

### [$ICPC\ WF\ 2019-交通堵塞$](https://loj.ac/p/6587)

令 $p_i = r_i + g_i$, $P = lcm(p_i)$，显然 $P\ |\ 2019!$。所以在 $[0, 2019!]$ 中随机实数 = 在 $[0, 2019!)$ 中随机实数 = 在 $[0, P)$ 中随机整数。

将某个出发时间 $t$ 表示为 $kX + a$ 的形式，其中 $X$ 是我们钦定的常数，$a \in [0, X)$。现在我们要解同余方程组 $kX + a + x_i \geq r_i\ mod\ p_i$，对于不同的 $a$ 分别处理（这里概率显然是可加的），就只要关心 $k$ 了。

设 $S$ 为满足条件的 $k$ 的 $kX + a$ 构成的集合，那么 $kX + a \in S\ mod\ p_i$ 变形为 $k \in S\ mod\ \frac{p_i}{gcd(p_i, X)}$。怎么合并解集呢？

对于两个同余方程，设解集分别为 $S_1$ 和 $S_2$, 若它们模数互质，则解集为 $S_1 \times S_2$。

所以取 $X = lcm(1, \cdots, 10) = 2520$，你会发现由于 $p_i \in [1, 100]$，所有 $\frac{p_i}{gcd(p_i, X)}$ 都只有一种因数！每种质数作为模数的解集大小乘起来就行。

数据范围很宽，大力三重循环枚举，复杂度 $O(100nX)$

[$Code$](https://loj.ac/s/1052240)

### [$Vim$](https://loj.ac/p/2687)

[大佬](https://www.cnblogs.com/Itst/p/10339605.html)

主要还是看大佬的吧！我说点自己的理解。dp 如何划分阶段？我们还是从左往右，规定：每个位置 $i$ 的贡献是从它出发直到到达下一个右边的位置的操作数（即从 $i$ 出发，跨越 $[i, i + 1]$ 的）。这个导致了线头 dp 的特点：“路径”在 dp 过程中不是连续的，而是许多个线头不断延长拼起来的。

[$Code$](https://loj.ac/s/1053317)

### [精准预测](https://loj.ac/p/3101)

正常做 2-sat，$0$ 为死，$1$ 为生，我们要计算从 $(0, x, 1)$ 到 $(T + 1, y, 1)$ 的个数，也就是减去到 $(T + 1, y, 0)$ 的个数，由于是 DAG，拓扑 + bitset 即可。注意要判自己是否合法。

## $\mathcal{2.2}$
---

### $NOI2017-泳池$

写在「线性递推学习笔记」里了。

## $\mathcal{2.3}$
---

### 九省联考2018

这两天写了 $T1$ ~ $T5$，题解在「九省联考2018 乱写」。

### [$围绕着我们的圆环$](https://uoj.ac/problem/453)

单写了。

## $\mathcal{2.4}$
---

### [$ -购票$](https://uoj.ac/problem/7)

$f_i = \min{f_j + (d_i - d_j)p_i + q_i}$, $d_i$ 是 $i$ 到根的距离。

这很斜率优化。于是推一波得到 $d_j > d_k$ 且 $\frac{f_j - f_k}{d_j - d_k} > p_i$ 时 $j$ 一定不被取到。

然而它真的能直接上斜率优化吗= = 不行！**凸壳的复杂度是均摊的**，你带个删除它就伪了。

一筹莫展。

考虑一条链的情况，可以 cdq 分治。这启发我们点分治——把一个点到根的路径划分为许多段分治重心到分治子树根的路径，维护每个分治重心到分治子树根的路径凸壳，以更新分治子树的答案。复杂度 $O(nlog^2n)$，点分治一只 log，凸壳上二分一只 log。

[$Code$](https://uoj.ac/submission/453320)

### [$Ynoi2012-人生巅峰$](https://www.luogu.com.cn/problem/P5527)

小清新。取模诶！根据抽屉原理，$2^{r - l + 1} > maxV(r - l + 1)$，即 $r - l + 1 > 13$ 时必然有解；剩下拿个 bitset dp 一下就好了。修改操作就预处理立方，树状数组记操作次数，查询时倍增。

### [石家庄的工人阶级队伍比较坚强](https://uoj.ac/problem/272)

写在「集合幂级数」。

## $\mathcal{2.5}$
---

### [喂鸽子](https://uoj.ac/problem/449)

每只鸽子都等价，信息只和集合大小有关。

$$ans = \sum\limits_{i = 1}^n (-1)^{i + 1} \binom{n}{i} \frac{n}{i} f_i$$

$f_i$ 表示喂了 $i$ 只鸽子，其中恰好有一只吃了 $K$ 粒玉米的期望步数。乘 $\frac{n}{i}$ 是为了把集合从全集缩到那 $i$ 只的集合。

$$f_i = \sum\limits_{j = 0}^{(i - 1)(K - 1)} (j + K) \frac{i \binom{j + K - 1}{j} g_{i - 1, j}}{i^{j + K}}$$

枚举分给其他没喂饱的鸽子的玉米数。$g_{i, j}$ 表示把 $j$ 粒玉米分给 $i$ 只鸽子，每只吃的玉米数小于 $K$ 的方案数。钦定最后一粒玉米一定喂给那只饱的鸽子。

$$g_{i, j} = (\sum\limits_{t = 0}^{K - 1} \frac{x^t}{t!})^{i} [x^j]$$

可以 NTT，也可以 dp + 组合数算一下，每次加入一粒玉米，方案数是总数减去会喂饱一只鸽子的方案数。$O(n^2K)$。~~可是 NTT 要 $O(n^2KlognK)$~~

[$Code$](https://uoj.ac/submission/453568)

[另一种 $O(n^2K)$ 神仙方法](https://www.luogu.com.cn/paste/yyell4b7)

## $\mathcal{2.6}$
---

### [复读机](https://uoj.ac/problem/450)

不难看出求的就是 $n!(\sum\limits_{i = 0}^{\lfloor \frac{n}{d} \rfloor} \frac{x^{id}}{(id)!})^K [x^n]$。$d$ 好小，有诡！

$d = 1$, $ans = K^n$

$d = 2$, 可以直接用 $\frac{e^x + e^{-x}}{2}$

$$ans = n!(\frac{e^x + e^{-x}}{2})^K[x^n]$$

$$= \frac{n!}{2^K} \sum\limits_{i = 0}^K \binom{K}{i} e^{(2i - K)x} [x^n]$$

$$= \frac{\sum\limits_{i = 0}^K \binom{K}{i} (2i - K)^n}{2^K}$$

$d = 3$, 单位根反演.

$$ans = n!(\sum\limits_{i = 0}^n [3 \mid i] \frac{x^i}{i!})^K [x^n]$$

$$= n!( \sum\limits_{i = 0}^n \frac{1}{3} \sum\limits_{j = 0}^2 \omega_3^{ij} \frac{x^i}{i!} )^K [x^n]$$

$$= \frac{n!}{3^K}( \sum\limits_{j = 0}^2 e^{\omega_3^jx} )^K [x^n]$$

二项式展开

$$= \frac{n!}{3^K} \sum\limits_{i = 0}^K \binom{K}{i} \sum\limits_{j = 0}^{K - i} \binom{K - i}{j} e^{(i + j\omega_3^1 + (K - i - j)\omega_3^2)x} [x^n]$$

$$= \frac{1}{3^K}\sum\limits_{i = 0}^K \binom{K}{i} \sum\limits_{j = 0}^{K - i} \binom{K - i}{j} (i + j\omega_3^1 + (K - i - j)\omega_3^2)^n$$

直接 $K^2$。

[$Code$](https://uoj.ac/submission/453603)

### [LJJ 学二项式定理](http://loj.ac/p/6485)

满满的套路啊= = 好像连生成函数都没用到

$$\sum\limits_{i = 0}^n \binom{n}{i} s^i a_{i\ mod\ 4} = \sum\limits_{t = 0}^3 a_t \sum\limits_{i = 0}^{n} [4 \mid i - t] \binom{n}{i} s^i$$

$$= \sum\limits_{t = 0}^3 a_t \sum\limits_{i = 0}^n \frac{1}{4} \sum\limits_{j = 0}^3 \omega_4^{(i - t)j} \binom{n}{i} s^i = \frac{1}{4} \sum\limits_{t = 0}^3 a_t \sum\limits_{j = 0}^3 \sum\limits_{i = 0}^n \omega_4^{ij}s^i\binom{n}{i}$$

$$= \frac{1}{4} \sum\limits_{t = 0}^3 a_t \sum\limits_{j = 0}^3 \frac{(\omega_4^js + 1)^n}{\omega_4^{tj}}$$

[$Code$](https://loj.ac/s/1059309)

WC 出成绩了，人都不想动。。。没有银诶。。被自己菜傻了，T1 做过类似的 /kk 然而当时脑子里一闪而过。还是知识体系太烂了。要做 CF 杂题，综合题不想放也可以做，但是要锻炼思维模式，不然永远是个暴力选手 /kel

### $XJOI1656$

#### $T1$

点减边、边的信息放父亲上——还差一个套路：点分树。做完了。

#### $T2$

卡在预处理“一个人不碰到一条线的走”上了。这就是个卡特兰数，你把朝左走和朝右走分别看成朝右走和朝上走就是个走网格问题了，然后关于线对称什么的做一做。概率就是来糊弄你的。$O(nm^2)$ 足矣。

#### $T3$

平面图最短路转对偶图最小割，这样限制就可以用网络流的方式解决（限制某条边必须不选就是限制对偶图的某两个点必须在同个集合里，反向 $\infty$ 边）

### [$CF878D$](https://www.luogu.com.cn/problem/CF878D)

神题。如果是 $01$ 就直接 bitset 取并/取交了。令 $f_{i, s}$ 表示 $s$ 集合里的生物是否存在一个属性 $\geq$ $i$ 号生物的对应属性。以取 $\max$ 操作为例，$c \geq \max(a, b)$ 当且仅当 $c \geq a$ 且 $c \geq b$，所以是取交；同理，取 $\min$ 就是取并。bitset 加速即可。

另一种方法是用二分的思想, $\geq lim$ 的为 $1$，$< lim$ 的为 $0$，那么 $\max$ 为取并，$\min$ 为取交。

学习「二次离线莫队」。

订正今天 T1、T2、T3。

## 感觉自己杂题真做太少了，数学真学太少了，其他的基础差不多都掌握了。以后就往这两个方向努力吧！

## $\mathcal{2.7}$
---

### [$ZJOI2018-$历史](https://uoj.ac/problem/374)

单写了。非常nb的题。

### [WC2020]-猜数游戏

都 1202 年了不会有人 0202 年的 WC 还没补吧不会吧不会吧（x

咕咕

### [WC2021]-斐波那契

都 1202 年了不会有人还学不会数学吧不会吧不会吧（x

咕咕

学了「线段树分治」。一个不知道为什么会有专属称谓的东西。

## $\mathcal{2.8}$
---

8 号啦 寒假！寒假！寒假！小提琴 运动 域名 模拟赛 年货 鸡鸭鱼鹅肉白切肉炸春卷凉拌海蜇皮 春晚 它们来了！！！

写了「线段树分裂」。

### [WD 与地图](https://www.luogu.com.cn/problem/P5163)

嘴不清楚了 /kk

划重点，**SCC**，不是无向图的连通分量。

考虑时光倒流，反转时间轴。

我们关注的其实是点。是每条边的**两个端点**什么时候开始处于同一个 SCC 中。如果求出了这个时间，就只要从晚到早做一遍权值线段树合并。现在要求这个时间。

怎么快速查询两点是否在同个 SCC 里？并查集，还要支持撤销。

考虑整体二分。要找每对点出现在同个 SCC 中的最晚时间，就加 $[mid + 1, r]$ 的边——准确来说，是在在已有的 SCC 基础上——如果要加入一条边 $(x, y)$，那实际加入的是 $(getfa(x), getfa(y))$，并且 tarjan 也只跑这一轮加入的 SCC 间边。跑完 tarjan，把已经在一个 SCC 的点对往右丢，还没在一个 SCC 的点对往左丢。分类完 $[l, r]$ 的边后，先往 $[l, mid]$ 递归（撤掉边，不撤并查集）以利用上一层中已加的 $[mid + 1, r]$ 的边，然后再撤销并查集并递归 $[mid + 1, r]$。这样每条边被操作 $logn$ 次。

~~我感觉先右再左好像复杂度也是对的，但是要重新加一遍，麻烦~~

tarjan $O(m)$，可撤销并查集 $O(logn)$，整体二分就是 $O(m log^2n)$ 的。线段树合并一只 log 非常愉快。

代码太tm难写了。。。重构 $* n$，封装大法好啊。

<details>
    <summary>令人窒息的code</summary>
    ``` c++
    #include <bits/stdc++.h>
    #define rep(i, x, y) for (int i = x; i <= y; i++)
    #define fi first
    #define se second
    #define pb push_back
    #define mkp make_pair
    #define mid ((l + r) >> 1)
    #define int long long
    using namespace std;

    typedef long long ll;
    typedef pair<int, int> pii;
    const int N = 4e5 + 10, inf = 1e9 + 1e3;
    int n, m, Q;
    int a[N];
    struct Ques {
        int op, x, y;
    } q[N];
    struct Edge {
        int x, y, t;  // 出现时间
        friend bool operator < (Edge a, Edge b) { return a.t < b.t; }
    } e[N];
    map<pii, int> id;

    namespace INIT {
        void solve() {
            cin >> n >> m >> Q;
            rep(i, 1, n) {
                scanf("%lld", &a[i]);
            }
            rep(i, 1, m) {
                scanf("%lld%lld", &e[i].x, &e[i].y);
                e[i].t = Q;
                id[mkp(e[i].x, e[i].y)] = i;
            }
            rep(i, 1, Q) {
                scanf("%lld%lld%lld", &q[i].op, &q[i].x, &q[i].y);
                if (q[i].op == 1) e[id[mkp(q[i].x, q[i].y)]].t = i - 1;
                if (q[i].op == 2) a[q[i].x] += q[i].y;
            }
        }
    }

    namespace Sol1 {
        int p[N], cnt, now;
        struct atom { int x, y, t; };
        struct DSU {
            int fa[N], sz[N];
            vector<atom> stk;
            DSU() {
                rep(i, 0, N - 1) fa[i] = i, sz[i] = 1;
            }
            int getfa(int x) {
                return fa[x] == x ? x : getfa(fa[x]);
            }
            void merge(int x, int y) {
                x = getfa(x), y = getfa(y);
                if (x == y) return;
                if (sz[x] > sz[y]) swap(x, y);
                stk.pb((atom){x, y, now});
                fa[x] = y, sz[y] += sz[x];
            }
            void undo(int t) {
                while (stk.size() && stk.back().t == t) {
                    atom o = stk.back(); stk.pop_back();
                    fa[o.x] = o.x, sz[o.y] -= sz[o.x];
                }
            }
        } dsu;  // 此处并查集维护 SCC

        vector<int> g[N];
        int dfn[N], low[N], ins[N], st[N], top, Dfn;
        void tarjan(int x) {
            dfn[x] = low[x] = ++Dfn;
            st[++top] = x, ins[x] = 1;
            for (int i = 0; i < g[x].size(); i++) {
                int y = g[x][i];
                if (!dfn[y]) {
                    tarjan(y);
                    low[x] = min(low[x], low[y]);
                } else if (ins[y]) {
                    low[x] = min(low[x], dfn[y]);
                }
            }
            if (low[x] == dfn[x]) {
                while (st[top] != x) {
                    ins[st[top]] = 0, dsu.merge(st[top], x), --top;
                } if (st[top] == x) ins[x] = 0, --top;
            }
        }

        Edge e1[N], e2[N];
        void solve(int l, int r, int L, int R) {
            if (L > R) return;
            if (l == r) {
                rep(i, L, R) e[i].t = l;
                return;
            }
            int ori = ++now, lst = cnt;
            rep(i, L, R) {
                int x = dsu.getfa(e[i].x), y = dsu.getfa(e[i].y);
                if (x != y && e[i].t > mid) {
                    g[x].pb(y);
                    p[++cnt] = x, p[++cnt] = y;
                }
            }
            Dfn = 0;
            rep(i, lst + 1, cnt)
                if (!dfn[p[i]]) tarjan(p[i]);
            int t1 = 0, t2 = 0;
            rep(i, L, R) {
                if (dsu.getfa(e[i].x) == dsu.getfa(e[i].y) && e[i].t > mid) e2[++t2] = e[i];
                else e1[++t1] = e[i];
            }
            rep(i, 1, t1) e[L + i - 1] = e1[i];
            rep(i, 1, t2) e[L + t1 + i - 1] = e2[i];
            rep(i, lst + 1, cnt)
                g[p[i]].clear(), dfn[p[i]] = low[p[i]] = 0, p[i] = 0;
            cnt = lst;
            solve(l, mid, L, L + t1 - 1);
            dsu.undo(ori);
            solve(mid + 1, r, L + t1, R);
        }
    }

    namespace Sol2 {
        struct SGT {
            #define D N * 22
            int idx, ls[D], rs[D], sz[D];
            ll sum[D];
            void modify(int &x, int l, int r, int p, int v) {
                if (!x) x = ++idx;
                sum[x] += v * p, sz[x] += v;
                if (l == r) return;
                p <= mid ? modify(ls[x], l, mid, p, v) : modify(rs[x], mid + 1, r, p, v);
            }
            ll query(int x, int l, int r, int k) {
                if (!x || !k) return 0;
                if (l == r) return min(k, sz[x]) * l;
                // if (sz[x] <= k) return sum[x];  // 这里为什么不对啊 /kk 摸不着头发
                if (sz[rs[x]] <= k) return sum[rs[x]] + query(ls[x], l, mid, k - sz[rs[x]]);
                else return query(rs[x], mid + 1, r, k);
            }
            void merge(int &x, int y) {
                if (!x || !y) { x += y; return; }
                if (!x) x = ++idx;
                sum[x] += sum[y], sz[x] += sz[y];
                merge(ls[x], ls[y]), merge(rs[x], rs[y]);
            }
        } seg;
        int rt[D];
        struct DSU {
            int fa[N];
            DSU() {
                rep(i, 0, N - 1) fa[i] = i;
            }
            int getfa(int x) {
                return fa[x] == x ? x : fa[x] = getfa(fa[x]);
            }
            void merge(int x, int y) {
                x = getfa(x), y = getfa(y);
                if (x == y) return;
                fa[y] = x, seg.merge(rt[x], rt[y]);
            }
        } dsu;
        ll ans[N];
        void solve() {
            memset(rt, 0, sizeof(rt));
            rep(i, 1, n) seg.modify(rt[i], 0, inf, a[i], 1);
            sort(e + 1, e + m + 1);
            int pos = m + 1;
            for (int i = Q; i; --i) {
                if (q[i].op == 1) continue;
                while (pos > 1 && e[pos - 1].t >= i) {
                    --pos, dsu.merge(e[pos].x, e[pos].y);
                }
                int x = dsu.getfa(q[i].x);
                if (q[i].op == 2) {
                    seg.modify(rt[x], 0, inf, a[q[i].x], -1);
                    a[q[i].x] -= q[i].y;
                    seg.modify(rt[x], 0, inf, a[q[i].x], 1);
                } else {
                    ans[i] = seg.query(rt[x], 0, inf, q[i].y);
                }
            }
            rep(i, 1, Q) if (q[i].op == 3) printf("%lld\n", ans[i]);
        }
    }

    signed main() {
        INIT::solve();
        Sol1::solve(0, Q, 1, m);
        Sol2::solve();
        return 0;
    }
    ```
</details>

# 寒假~

## *养生实录*

计划！

### 🏃‍♀️🏊‍♀️

<table border="1">
  <tr>
    <th>Date</th>
    <td>2.9</td>
    <td>2.10</td>
    <td>2.11</td>
    <td>2.12</td>
    <td>2.13</td> 
    <td>2.14</td>
    <td>2.15</td>
    <td>2.16</td>
    <td>2.17</td>
  </th>
  <tr>
    <th></th>
    <td></td>
    <td>1.5km</td>
    <td>30min</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </th>
</table>

### 阅读

<table border="1">
  <tr>
    <td>《玛丽》</td>
    <td>《微暗的火》</td>
  </th>
  <tr>
    <td></td>
    <td></td>
  </th>
</table>

&radic;

### 小提琴

### 域名
:(
    
### whk
作业干完了

### 具体数学

## *训练实录*

打 vp 做杂题 / 做集训队作业题、清华集训题。

学习 BSGS。

学习李超线段树。

学习多元拉反。

写 Ynoi。

做 LCT。

gym102759G

## $\mathcal{2.9}$
---

## $\mathcal{2.10}$
---

### T2. Gym102759H

逆向思维，考虑一个 k 变成 [0, k - 1]，k - 1 再变成 [0, k - 2]... 而一个 0 可以变成任意整数。考虑尽量往 0 变，

T3: s[1, i] -> s[1, p(i)]
1. p(i) <= i / 2 (logn 次)
2. p(i) 较大
    形成 i - p(i) 的周期