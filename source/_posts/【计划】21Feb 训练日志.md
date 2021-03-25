---
title: '21Feb 训练日志'
date: 2021-02-28 23:59:59
tags: 
mathjax: true
encrypt: true
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
                if (l == r) return min(k, sz[x]) * l;  // !!! 应该是这个
                // if (sz[x] <= k) return sum[x];  // 这个不对！在叶子处就错了
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
    <td>/</td>
    <td>1.5km</td>
    <td>30min</td>
    <td>1km</td>
    <td>/</td>
    <td>乱跑</td>
    <td>1km</td>
    <td>1km</td>
    <td>乱走</td>
  </th>
</table>

### 阅读

<table border="1">
  <tr>
    <td>《玛丽》</td>
    <td>《微暗的火》</td>
  </th>
  <tr>
    <td>?</td>
    <td>?</td>
  </th>
</table>

### 小提琴

&radic;

### 域名
:(
    
### whk
作业干完了

## *训练实录*

学习多元拉反。

写 Ynoi。

做 LCT。

## $\mathcal{2.9}$
---

## $\mathcal{2.10}$
---

[比赛链接](https://vjudge.net/contest/422521#overview)

### $B$

逆向思维，考虑一个 $k$ 变成 $[0, k - 1]$，$k - 1$ 再变成 $[0, k - 2]$... 而一个 $0$ 可以变成任意整数。考虑尽量往 $0$ 变，根据「$0$ 的个数是否足以填上空缺」来二分（单调性显然：随着 $k$ 增大，$0$ 的个数递增，需要填补的位置减少，$\geq$ 号更容易成立）。

### $C$

SAM + LCT 碾过去，其实应该用 KMP 树（ACAM 那个）。。

[官方题解](https://drive.google.com/file/d/1ymS9INds6_w06CobKjP0QJILuM2oTNp4/view)：KMP 树上，如果「$x - fa_x = fa_x - fa_{fa_x}$」，把 $x$ 视为重儿子那么一个点到根的路径上至多有 $log$ 条重链。

证明：

$s[1, i] \rightarrow s[1, p(i)]$

1. $p(i) \leq i / 2$ (至多 $log$ 次)
2. $p(i)$ 较大，形成 $i - p(i)$ 的周期，就一条重链到顶了

树剖，每条重链上维护一棵 BIT 记录前缀子树 size。官方：再分析分析发现这是一只 $log$ 的。我：谢谢 爪巴了

### $D$

用 prufer 结论 $n^{k - 2} \prod s_i$ 解决此题。$\prod s_i$ 可以 dp。先用同色点拼成 $\leq m$ 的连通块。但是直接做会造成两个同色连通块拼起来 $> m$ 的情况，考虑容斥。只有同色的需要容斥，所以要对每种颜色单独处理并乘上容斥系数。

- $f_{i, j}$：同色的 $i$ 个点组成 $j$ 个连通块的 $\prod s_i$ 之和
- $sum_i$：同色的 $i$ 个点组成连通块的带容斥系数的 $\prod s_i$ 之和
- $g_{i, j}$：同色的 $i$ 个点组成 $j$ 个连通块的带容斥系数的 $\prod s_i$ 之和
- $h_{i, j}$：前 $i$ 种颜色共有 $j$ 个连通块的带容斥系数的 $\prod s_i$ 之和

最后用 $h$ 做 prufer。

### $E$

### $F$

平面图最大流转最小割转对偶图最短路。

考虑与最外面相邻的边，设其中权值最小的为 $e$，$e$ 靠里那一面会被经过 $0$ 次或 $2$ 次，$2$ 次就一定经过 $e$。于是干脆把 $e$ 的权值分到这个面其他边上，$e$ 消失——用堆维护上述过程，每次除一个环，最终得到一棵树。

再回到平面图最小割的角度看，类 kruskal 重构树的维护一棵最小割树就可以了。

## $\mathcal{2.13}$
---

[比赛链接](https://vjudge.net/contest/422627#overview)

### $B$

赛时胡个结论 A 了此题：必定存在一个机器人 $i$ 和天线 $i$ 直接对应（感性理解：一个天线 $i$ 控制一个连续区间的机器人，两端的天线必然满足上面那句话）。于是先炸这些机器人。它们的爆炸影响了相邻两个机器人与天线的对应关系，将新的「满足直接对应关系」的机器人入队即可。链表维护。

### $C$

每条线段最多经过两次。原始对偶算法跑费用流。因为本题是 DAG，第一次 spfa 可用 dp。

### $D$

网格图——分治。二分，利用特殊性质里面第三条，询问 $3n + 12logn$ 次。

### $E$

trick，动态维护背包，每次删除最小的元素。构建两个栈，pre 向左，suf 向右，删从 pre 删，加从 suf 加，pre 删没了就重构——把 suf 的元素都丢到 pre 里。均摊下来每个元素出入各一次，出入复杂度 O(mod)。区间那个用

### $F$

写在「生成函数」。

## $\mathcal{2.14}$
---

[比赛链接](https://vjudge.net/contest/422937)

*你秒了 D，他切了 E，我卡 C 卡不进去。我们都有光明的未来！*

### $C$

**原因：vector 太慢了，改成数组 AC，15s -> 1s (;´༎ຶД༎ຶ`) [贴一个别人的效率测评](https://www.cnblogs.com/sdltf/p/12841544.html)**

分治乘，把一个多项式 $a$ 拆成 下标分别以 $0$、$1$、$2$ （三进制下）为第一位的三个多项式 $a0$, $a1$, $a2$，$(a0, a1, a2) * (b0, b1, b2) = ((a1 + a2) * (b1 + b2), (a0 + a1 + a2) * (b0 + b1 + b2) - (a1 + a2) * (b1 + b2) - (a0 * b1 + a1 * b0), a0 * b1 + a1 * b0)$, $T(k) = 3^k + 4T(k - 1)$，$O(4^k)$。

### $D$

若点权均非负，一棵树可能的带权重心在一条链上，其中深度最小的点是满足「子树点权和严格大于总和一半」的点中深度最大的。因为其子树和大于一半，该树任何一个 dfs 序的带权中位数一定在其子树中。因此找到带权中位数的点后倍增上跳即可。

### $E$

根据可达关系缩点（每个横向分量和纵向分量分别以右端和下端为特征点），发现一个星被收集当且仅当它横向分量或纵向分量至少有一个被经过。问题转化为能否选择一条合法路径（即点集）。具体来说限制如下：

1. 每个星对应的横向和纵向分量至少有一个被经过
2. 点集内点两两可达
3. 点集内点从起点可达

### $F$

## $\mathcal{2.15}$
---

[比赛链接](https://vjudge.net/contest/422997#overview)

### $B$

当前讨论以 $0$ 为下标。只会 $n$ 为奇数：$p_{i, j} = (2^i + j)\ mod\ n$。

$n$ 为 $4$ 的倍数只要将 $p_0$ 构造成每四个分一组，$(i + 2, i + 3, i + 1, i)$ 这样即可，每个位置 $* p_0$ 或 $p_0^{-1}$ 都能到达奇数位或偶数位，剩下 $K - 1$ 个置换同 $n$ 为奇数时 $p_0$ ~ $p_{K - 2}$。

$n = 2$ 无解，否则 $n \equiv 2 \pmod 4$ 需要：$p_0 = (2, 3, 1, 4, \cdots, n - 3, n - 2, n - 4, n - 5, n - 1, n)$, $p_1 = (1, 2, 3, \cdots, n - 1, n, n - 2, n - 3)$，剩下 $K - 2$ 个置换同 $n$ 为奇数时 $p_0$ ~ $p_{K - 3}$

### $C$

### $D$

考察平行求和法（不懂左转【置顶】）。统计 $f(T)$ **至少**是 $d$ 的贡献，枚举 $d$ 和 $|S| \in [1, n/d]$，$O(nlogn)$

### $E$

[dalao博客](https://www.cnblogs.com/cjoierShiina-Mashiro/p/12955361.html)

那个下取整里面的 $/ d$ 是为了用类欧几里得搞事情。学习「类欧几里得」！

### $F$



## $\mathcal{2.16}$
---

[比赛链接](https://vjudge.net/contest/423084#overview)

### $C$

先不考虑取模。设答案为 $K$，那么 $a_i \leq K$, $a_i + a_{i + 1} \geq K$ 显然是必要条件。有了这个条件我们也能构造方案：对每个 $i$ 把 $a_i$ 个放在 $i$ 上，把 $K - a_i$ 放在 $a_{i + 1}$ 上即可。也就是说当 $\max(a_i) \leq \min(a_i + a_{i + 1})$ 时 $K = \max(a_i)$。

考虑取模，每次取**最小的** $a_i$ 加上 $mod$ 变成最大的，再判是否满足 $\max(a_i) \leq \min(a_i + a_{i + 1})$。set 维护，$O(n + nlogn)$

### $D$

看代码，hhz 写了复杂度 $O(n^2 * 4)$ 的最短路，$dis[x, y, dir]$ 表示从 $dir$ 方向来到 $(x, y)$，最大的剩余能量。

### $E$

**每个格子只能是 $0$ 或 $1$。** ~~（juruo xml 竟然忘了这个！）~~

$a_{x, y} + a_{x + 4, y + 4} = a_{x + 4, y} + a_{x, y + 4}$，这个利用 $a_{x, [y + 1, y + 3]}$ 和 $a_{x + 4, [y + 1, y + 3]}$ 可以推得。并且有 $\sum\limits_{i = 0}^3 a_{x, y + i} = \sum\limits_{i = 0}^3 a_{x + 4, y + i}$，$\sum\limits_{i = 0}^3 a_{x + i, y} = \sum\limits_{i = 0}^3 a_{x + i, y + 4}$。根据抽屉原理，必然有 $a_{x, y} = a_{x + 4, y}$ 或 $a_{x, y} = a_{x, y + 4}$。讨论发现不存在 $a_{x, y} = a_{x + 4, y} = a_{x + 4, y + 4} \neq a_{x, y + 4}$ 的情况，即下面两种情况至少满足其一：

- $a_{x, y} = a_{x + 4, y} = a_{x + 8, y} \cdots$
- $a_{x, y} = a_{x, y + 4} = a_{x, y + 8} \cdots$

然后是一些奇怪的容斥，没太懂 (｡ ́︿ ̀｡) 大概是容斥掉因为两个条件都满足而被重复算了的方案。

### $F$

无人提交，先咕咕

## $\mathcal{2.17}$
---

[比赛链接](https://vjudge.net/contest/423187#overview)

### $B$

将 LIS 的关系建成 DAG，就是求 DAG 支配树上每个节点的深度，正反各求一遍。

按深度分层，每层高度递减，求一段后缀 $[x, tail]$ 的 LCA 只需求 $x$ 和 $tail$ 的 LCA 即可。

### $C$

考虑可以左移的情况，等价于每次移一个字符，$ans = n - LCS(s, t)$ 

考虑不能左移的情况，$s_i$ 与 $t_j$ 匹配当且仅当 $[i, n]$ 和 $[j, n]$ 中任何一种字符的个数都是 $s$ 比较多。

### $D$

脑子里全是「总数 / 方案数」。。然而为了切小数版本，只能用概率做。

$f_{i, S}$ 表示前 $i$ 步死了 $S$ 集合里的人的概率，$g_T$ 表示除去 $S$ 集合的人，死了 $T$ 集合人的方案数。

### $E$

从高位往低位取，用霍尔定理推论算最大匹配。

原集 Or 转为补集 And 做 FMT。

### $F$

无人提交，先咕咕

## $\mathcal{2.19}$
---

### [$Parametric\ Circulation$](https://www.luogu.com.cn/problem/CF925F)

写在「网络流」。

### [点双连通子图计数](https://loj.ac/p/6729)

写在「集合幂级数」。

### [边双连通子图计数](https://loj.ac/p/6730)

写在「集合幂级数」。

### [$CF891E-Lust$](https://www.luogu.com.cn/problem/CF891E)

写在「生成函数」。

### [胡策的统计](https://uoj.ac/problem/94)

写在「集合幂级数」。

### [$Mole\ Tunnels$](https://www.luogu.com.cn/problem/P6122)

简单模拟费用流，在完全二叉树上暴跳，注意记录对反向边的影响。$O(nlogn)$

## $\mathcal{2.20}$
---

### $XJOI1657$

#### $T1$

减法卷积板题，没有 AC 的奇怪原因是没想到把 $(1 - p_{i - 1})$ 和 $(1 - p_{i + k})$ 乘进去。

$h_{i - 1 - j} += (1 - p_i) \prod\limits_{k = 0}^{i - 1} p_k * \frac{1 - p_j}{\prod\limits_{k = 0}^{j} p_k}$

令前面那个为 $f_{i - 1}$，后面那个为 $g_{n - j}$。化一下：

$h_{i - j + n} += f_{i} * g_{n - j}$

FFT 即可。

#### $T2$

两维分开做，拍扁成区间。每个区间选左端点还是右端点不变，且一旦它不选（被包含在上一个区间里了），之后它都不会被选，所以维护一个链表。定义「事件」为链表中相邻两项相碰，后面那个被 ban，显然「事件」只会发生 O(n) 次。

#### $T3$

大概二次函数也能做吧但是很损精度。正解是一个挺显然~~但是 xml 没猜~~的结论：一定选最大或最小值把它变成剩余数的平均值。需要维护最大最小值、区间和、区间平方和。fhq-treap 即可。

我的二次函数荣获 5 分因为最值是 $\frac{-b^2}{4a}$ 有平方啊啊，我 sb 只考虑最大值就 GG 了。改后 AC 我直接痛哭流涕被教做人！！！！！1

### [$Son\ of\ pipe\ streams$](https://loj.ac/p/6479)

写在「网络流」。

### [如何优雅地求和](https://uoj.ac/problem/269)

把 $f(x)$ 变成下降幂形式。设 $f(x) = \sum\limits_{i = 0}^x \binom{x}{i} s_i$，由 NTT 加速二项式反演得到 $s_i$。

$$\sum\limits_{k = 0}^n \sum\limits_{i = 0}^k s_i \binom{k}{i} \binom{n}{k} x^k (1 - x)^{n - k}$$

$$= \sum\limits_{i = 0}^m s_i \binom{n}{i} x^i \sum\limits_{k = 0}^{n - i} \binom{n - i}{k} x^k (1 - x)^{n - k - i}$$

$$= \sum\limits_{i = 0}^m s_i \binom{n}{i} x^i$$

总之就是下降幂和组合数结合起来搞事情。

### [组合数问题](https://www.luogu.com.cn/problem/P6620)

写在「联合省选2020 乱写」。

### [奥林匹克环城马拉松](https://uoj.ac/problem/226)

考虑把无向图转化成有向图用 BEST 定理做。

由于有向图欧拉回路中每个点 $in_deg = out_deg$，枚举任意一条环上边往一个方向走了几次，就能确定剩余环边的度数，组合数算方案。树形图计数就把环和环上树的答案乘起来。$O(t_in)$

[$Code$](https://uoj.ac/submission/456790)

### [ARC112-C](https://atcoder.jp/contests/arc112/tasks/arc112_d)

就是个坑对手的问题。奇数大小的子树可以改变先后手，必定从小的开始选；偶数大小的若 $\leq 0$ 那必然选，若 $> 0$ 则要看奇子树个数，若为奇则减，否则加。

### [ARC112-D](https://atcoder.jp/contests/arc112/tasks/arc112_d)

很像[这道](https://vjudge.net/problem/Gym-102059B)。定义一行/列是好的当且仅当能停在这行/列上的某些位置。那么墙和 ground square 就成了连接行/列的边。$ans = \min(行形成的连通块个数, 列形成的连通块个数) - 1$

### [ARC112-E](https://atcoder.jp/contests/arc112/tasks/arc112_e)

$dp_{i, l, r}$ 表示操作了 $i$ 步，左边堆了 $l$ 个，右边堆了 $r$ 个（必要的）。三种情况：$l++$, $r++$, 没的加。注意没得加的时候要乘上系数 $2 * dp_{i, l, r} * (l + r)$。

三方的怎么优化？发现转移时乘的系数只与 $l + r$ 有关，于是设 $j = l + r$ 变成两维，最后算答案再乘上组合数 $\binom{l + r}{l}$ 即可。

### [$Sum$](https://uoj.ac/problem/42)

写在「类欧几里得」。

## $\mathcal{2.21}$
---

困死了困死了困死了困死了 天大地大睡觉最大awa 每天中午都睡不着，清醒的很，今天更好了，脑内循环 xyf 那句“啊♂ myy” ——疯了，又疯了一个

cpu风扇飞转起来了，电脑开始轰鸣了，春天到了。

### $XJOI1666$

$T1$、$T2$ sb 题。$T2$ 是要一点点小结论的套路题，还是做了很久，拐进了 n 多个死胡同，果然要多 vp AT！

#### $T3$

结论是求图的最大生成树作为最小割树。

### 李超线段树

刚学。

### [玛里苟斯](https://uoj.ac/problem/36)

大家都知道初步怎么入手，但是理智推柿不容易 go wrong。

$$ans = E(XOR(A)^k)$$

$$= E( (\sum\limits_i bit(XOR(A), i)2^i)^k )$$

$$= \sum\limits_{i_1 \geq 0, \cdots, i_k \geq 0} 2^{\sum\limits_{j = 1}^k i_j} E( \prod\limits_{j = 1}^k bit(XOR(A), i_j) )$$

问题变成求每个子集 $T$，异或值中 $T$ 中的每一位都为 $1$ 的集合 $A$ 个数。~~然后就卡了，不知所措~~

异或，考虑线性基，建出**只有 $T$ 中二进制位**的 **$S$ 中所有数** 的线性基，如果不能异或出 $T$ 中所有数，答案为 $0$；否则答案为 $2^{自由元个数}$。

算出所有 $|T| \leq k$ 的答案即可。$k$ 最大只有 $5$，$\binom{63}{5}$ 可海星。

[$Code$](https://uoj.ac/submission/456913)

### $AGC034$

[指路](https://imilyx.github.io/2021/02/21/AGC%20034%20%E9%A2%98%E8%A7%A3/)

## $\mathcal{2.22}$
---

mac 老是乱叫，烦死了。你再叫就给你丢到海里去喂鲨鱼嗷听见没！

每日感想：我的关节真好玩！~~就是打响指啦~~

[vjudge比赛链接](https://vjudge.net/contest/423849#overview)

### $A$

把无向图 dfs 树的欧拉序划分给 $k$ 个人。

### $B$

倍增啊，$dp_{0/1, i, x, y}$ 表示能否以 $0$/$1$ 开始，从 $x$ 走 $2^i$ 步到 $y$。bitset 优化。

### $C$

以前写的。

### $D$

「数据结构题，满分，正常。」

写了两只 log，线段树 + set。容易发现每块板只分裂出左右两个点，点数是 $O(n)$ 的。

找后继节点就把 $u_i + s_i$、$u_i$ 混在一起从上往下扫，扫到 $u_i + s_i$ 就在线段树上 $[l_i, r_i]$ 的 set 丢一个 pair $(u_i, i)$。查询就，，一路上查呗。拓扑 dp 即可。

**注意：有些访问不到的节点要从 DAG 中去掉，不然后序 deg 无法清空。**

一只 log 的做法是按 $u_i$ 从小到大，线段树每个节点开单调栈维护 $u_i + s_i$。

### $E$

以前写的。

### [$CF715E$](https://www.luogu.com.cn/problem/CF715E)

考虑两个排列的距离怎么算，把 $p_i \rightarrow q_i$ 看作置换，距离就是把置换排序的最小代价，即 $n - 置换循环个数$。那么 $ans_k$ 就是补全一些链后循环数为 $n - k$ 的方案。

把链规约为更简单的形式: $x \rightarrow 0$, $0 \rightarrow x$, $0 \rightarrow 0$, 和一些完整的循环。完整循环可以不考虑。

现在每种置换都有这么几种存在形式：

- $x \rightarrow 0$ 独自成环（$A$）
- $0 \rightarrow x$ 独自成环（$B$）
- $x \rightarrow 0$、$0 \rightarrow x$、$0 \rightarrow 0$ 拼接而成（$C$）

三个分别算，卷起来就好了。设这仨分别为 $F$, $G$, $H$。$f_i$ 和 $g_i$ 求法相同。

怎么求 $f_i$？观察发现，$x \rightarrow 0 + x \rightarrow 0 = x \rightarrow 0$，$0 \rightarrow 0 + x \rightarrow 0 = 0 \rightarrow 0$。于是枚举用到 $A$ 里的 $x \rightarrow 0$ 个数，剩下的逐个拼，最后全拼成 $0 \rightarrow 0$ 型。最终剩下仍旧是 $cnt_{0 \rightarrow 0}$ 个 $0 \rightarrow 0$，刚好做成 $C$。

$f_i = \sum\limits_{j = i}^{cnt_{x \rightarrow 0}} \binom{cnt_{x \rightarrow 0}}{j} {j \brack i} ( cnt_{x \rightarrow 0} + cnt_{0 \rightarrow 0} - j - 1 )^{ \underline{cnt_{x \rightarrow 0} - j} }$

$h_i = {cnt_{0 \rightarrow 0} \brack i} cnt_{0 \rightarrow 0}!$

$0 \rightarrow 0$ 还没分配标号呢，要乘 $cnt_{0 \rightarrow 0}!$ ~~我就忘了~~

[$Code$](https://www.luogu.com.cn/record/46924710)

### [榕树之心](https://uoj.ac/problem/345)

看到名为 Fuyuki 的大佬提供了一种强无敌的思路，严谨清晰逻辑严密，很有数学家的浪漫气息（什么鬼）本质还是一样的。

对于每个节点 $u$ 求一个 $f_u$，$x \in f_u$ 当且仅当存在一种 $u$ 子树内的操作方法使得子树外的心向 $u$ 子树内移动 $x$ 步。有：

$$f_u = \cup_{x_v \in f_v} | \sum \pm (x_v + 1) |$$

这个 $\pm$ **取决于**开始把心往子树 $v$ 里移时心在子树 $u$ 外还是内。

精妙的归纳：若原来 $f_u$ 是一段连续的奇偶性相同的数，加上 $f_v$ 的贡献后依然是一段连续的奇偶性相同的数。对于叶子节点有 $f_u = \{0\}$，归纳成立。

于是记录 $l_u$ 和 $r_u$ 即可转移。加贡献是这样的：

$$a + b = ( \max( (a.l + b.l) \& 1, \max(a.l, b.l) - \min(a.r, b.r) ), a.r + b.r )$$

这个 $\max(a.l, b.l) - \min(a.r, b.r)$ 讨论发现是对的（一个 $l$ 大于另一个 $r$，和两个 $l$ 都小于对方 $r$）。

求每个点，换根做一遍。

[$Code$](https://uoj.ac/submission/457061)

### [主旋律](https://uoj.ac/problem/37)

正难则反。但是不会切入。

一个非强连通的连通图缩点后是个 DAG，考虑枚举所有 SCC 缩点情况，状压 dp 计算，$f_S$ 表示 $S$ 集合的 SCC 构成 DAG 的方案数，枚举新加入的点集，因为不能精准的让每个点都连出边，需要容斥：$f_S = \sum\limits_{T \subseteq S} (-1)^{|T| + 1} 2^{e_{T \rightarrow S - T}} f_{S - T}$，但是枚举缩点、处理 $e$ 的复杂度太高了，而且 $O(m3^n)$ 也完全不行。

我们发现答案只跟 SCC 个数的奇偶性有关。

于是没必要枚举缩点了，设 $f_S$ 表示 $S$ 集合的点能组成一个 SCC 的子图数，$g_S$、$h_S$ 分别表示点集 $S$ 组成奇/偶数个 SCC，且两两之间无连边的子图数

$$f_S = 2^{e_{S \rightarrow S}} + \sum\limits_{T \subset S} (h_T - g_T) 2^{e_{T \rightarrow S - T} + e_{S - T \rightarrow S - T}}$$
$$g_S = \sum\limits_{T \subseteq S, T \neq S} h_T * g_{S - T}$$
$$h_S = \sum\limits_{T \subseteq S, T \neq S} g_T * h_{S - T}$$

[$Code$](https://uoj.ac/submission/457078)

### [你的生命已如风中残烛](https://uoj.ac/problem/273)

* Raney引理：如果 $a_1, \cdots, a_n$ 是一个和为 $1$ 的整数数列，那么 $a$ 的所有循环表示中仅有一种满足所有前缀和为正。画折线图即可证。（《具体数学》p301）

原题中是 $\sum a_i = 0$，且前缀和非负，我们不能直接这么做。具体数学中有个类似例子是 $n$ 个 $+1$ 和 $n$ 个 $-1$，解决方法是在开头加一个 $+1$ 转化为 Raney 问题，但是本题不能这么做因为中途会有比 $+1$ 大的，无法保证 $+1$ 一定在开头。

很妙的解法：在末尾加一个 $-1$，要求前缀和非负。每个后缀和都 $\leq -1$，取反再逆序就转化为 Raney 问题，方案数 $\frac{m!}{m - n + 1}$ 因为长度为 $m + 1$ 的序列圆排列个数是 $m!$，每个圆排列仅有一种循环表示满足限制；$-1$ 有 $m - n + 1$ 个。

## $\mathcal{2.23}$
---

[比赛链接](https://vjudge.net/contest/424037)

$A$ 水题，$B$ 做过

### $C$

压缩（旋转、对称、镜像。。），取最小表示作为代表元。

### $D$

考虑增量，是 + split - merge。并查集维护，merge 正着做，且只考虑当前加入颜色；split 反着做，且忽略当前加入颜色。vector 记录历史颜色。~~巨慢无比，把 $3$ 个 $nm$ 中的一个从 $4$ 个方向改为 $2$ 个方向，分别 3992ms 和 3946ms 低空飞过~~

~~哇！这题难度评级竟然有 2800！~~

又好写又快的方法是单独考虑每种颜色的贡献。~~线段树分治是完全不行的~~

### $E$

用李超树做，板题

xml 的每日 sb：不能直接凸包，因为如果你先构造出全局凸包再查询，无法避免掉自己子树；如果你一个一个子树加过去，*造凸包必须是 $x$ 坐标递增*，就会出现「在中间加点」的情况。动态凸包据说是可以的。

hhz：更好更快的 $logn^2$ 凸包做法：分治，把点分治重心的子树集合划成两半就可以规避掉自己对自己的贡献。这样，一层中每条路径被加入凸包 $logn$ 次，共有 $logn$ 层。（为什么我觉得是一只 $log$ 的？）

维护上凸包因为要求最大嘛，发现有单调性，一遍滚过去就行了。

### $AGC\ 035$

[指路](https://imilyx.github.io/2021/02/23/AGC%20035%20%E9%A2%98%E8%A7%A3/)

### [$LOJ6496-仙人掌$](https://loj.ac/p/6496)

仙人掌感觉就是很多环和树拼起来？用圆方树可以完美 ko！

考虑圆方树
1. $x$ 圆，父亲圆

    $f_{x, 0/1}$ 表示 $x$ 的父边是否指向 $x$
2. $x$ 圆，父亲方

    $f_{x, 0/1/2}$ 表示与 $x$ 直接相连的两条边指向 $x$ 的个数
3. $x$ 方，父亲圆

    $f_{x, 0/1/2}$ 表示 $x$ 代表的环的最上面两条边**不指向** $x$ 父亲的个数

转移：
1. $x$ 圆

    $f_{x, 0} = \sum\limits_{v_1 + \cdots + v_k \leq a_x} \prod\limits_{y_i} f_{y_i, typ_{y_i} - v_i}$

    其中 $y$ 为圆时 $typ = 1$，否则 $= 2$。
    $f_{x, 1}$ 类似。

2. $x$ 方

    顺着环递推。（所以并不用真的建方点！）注意要先处理完环上树，然后递推的时候就枚举环上边的方向即可。

把 $x$ 每个儿子的答案卷起来这个小背包，需要分治卷呢！~~口区~~

[Code](https://loj.ac/s/1074961)

## $\mathcal{2.24}$
---

[比赛链接](https://vjudge.net/contest/424196)

### $A$

由叉积得，答案只和奇偶有关。设 $(i, j)$ 是合法对，$s_i \oplus s_j \oplus s(i, j) = 0$ 则 $s_j = s_i \oplus s(i, j)$，记录一下每种奇偶的个数即可。

### $B$

画成树的形式比较好理解。要么是选一堆浅点跳到它们，要么是选一个没点的位置作 lca 全跳到它，两种情况分别从小到大选。

### $C$

三分套三分？我爬了~ 总之宽度是单峰，固定宽度后左边界的坐标也是单峰。check 答案就考虑这是个**凸包**，只要找到上下凸壳和「左边界 + 宽度」这条右边界的交点就可以计算高度。

### $D$

不会。没有发现性质：一直操作**同一个 $i$**，可以让所有数字经过 $i$。但是发现 $0$ 经过 $i$ 的时候卡住了。

考虑避开 $0$ 的影响。把它放到 $n - 1$。接下来从 $n - 2$ 开始倒着处理下来，在 $n - i - 1$ 位置不断操作直到 $i$ 经过它——显然一定会经过。

现在我们得到一个递减序列。倒着处理每个 $i$ 直到 $p_i$ 为 $0$，这时 $[i, n - 1]$ 是递增序列。

### $E$

很神~~其实没那么神~~的 dp 题。

题目好像读错了？从右往左不是把提示反转过来。

先只考虑向右的提示。考虑完全包含之类的关系。提示 $x$ 在第 $i$ 位后若能转化为提示 $y$，则 $x$ 就没用了，于是有状压：$f_{s, x, i}$ 表示已经到达的提示集合为 $s$，当前在提示 $x$ 第 $i$ 位的序列长度最小值。

再考虑向左的提示，多加两维就好。

### 三维立体混元劲

咕咕

### [$CF1010F-Tree$](https://www.luogu.com.cn/problem/CF1010F)

权值是唬你的，考虑差分, $B_i = A_i - \sum\limits_{v \in son(i)} A_v$，$\sum B_i = X$，$B_i \in [0, X]$，一个大小为 $p$ 的连通块方案数就是 $\binom{X + p - 1}{p - 1}$

现在问题是计算大小为 $p$ 的包含 $1$ 的连通块数。朴素 dp 是 $n^2$ 的。容易列出生成函数柿子 $F_x = z \prod F_y + 1$，但是没法优化。

考虑 dp 上界是 $size_x$，有没有类 dsu on tree 复杂度的做法啊？

用花朵的链分治！先处理重链节点的轻儿子，再在重链上卷。

$$F_x = (((a_1 + 1)a_2 + 1)a_3 + 1)...a_k + 1$$

展开得

$$F_x = a_1a_2...a_k + a_2a_3...a_k + ... + a_k$$

设 $G_{l, r} = a_l...a_r + ... + a_r, F_{l, r} = a_l...a_r + ... + a_r$，则 $F_{l, mid} + F_{mid + 1, r} * G_{l, mid}$, $G_{l, r} = G_{l, mid} * G_{mid + 1, r}$

分治 NTT 即可。

坑点：不是完全二叉树

[$Code$](https://codeforces.com/contest/1010/submission/108370629)

### [$LOJ6198-$谢特](https://loj.ac/p/6198)

套路题。反建 SAM，查询就在 parent 树上遍历，对每个 SAM 节点维护一棵 Trie，插入 $w$，计算当前节点 $x$ 和 $x$ 子树中某点的贡献。由于此时的 LCS 固定，只要查询 $\max\limits_{y \in subtree(x)}(w_x \oplus w_y)$。可以启发式的在一棵 Trie 上查询和另一棵 Trie 的异或最大值，启发式合并 Trie。~~感觉启发式只要用了都是对的，这是好的！~~ ~~一遍过样例 + AC 这也是好的~~

写 SAM 真的好爽啊！！！！！

合并一只 $log$，查询应该是 $n * logn * logV$ 吧。

[$Code$](https://loj.ac/s/1076075)

### [$CF102769L-Lost\ Temple$](https://codeforces.com/gym/102769/problem/L)

神啊，一直动它不得。

---以下是出题人 Cyanic 的题解---

**关键性质：相邻两列答案至多差 $1$**。判断第 $i$ 列能否在 $x$ 年后消失，就看第 $i$ 列能否塞下规模为 $x$ 的菱形。会受到四个方向的约束，转化为 $4$ 次 RMQ 问题。

考虑在上一列的基础上暴力枚举答案。另外发现 RMQ 询问的区间大致单调，可以使用单调队列，零散的段暴力。需要同时维护 $4$ 个单调队列和 $8$ 个指针。

---题解完毕---

其实就是对第 $i$ 列找一个区间 $[l, r]$ 使得 $ans = \frac{r - l}{2}$ 尽量大，且 $\forall j \in [0, ans]$, $[l + j, r - j] \subseteq [l_{i - j}, r_{i - j}], [l_{i + j}, r_{i + j}]$（两个都包含）

即 $l \geq \max(l_{i - j}, l_{i + j}) - j$, $r \leq \min(r_{i - j}, r_{i + j}) + j$，即 $ans$ 合法当且仅当

$$\min\limits_{j \in [0, ans]}( \min(r_{i - j}, r_{i + j}) + j ) - \max\limits_{j \in [0, ans]}( \max(l_{i - j}, l_{i + j}) - j ) \geq 2ans$$

记上一列的答案为 $ans$，那这次只要判定 $ans \pm 1$ 即可。

<details>
    <summary>code</summary>
    ``` c++
    void gen(int n, int L, int X, int Y, u64 A, u64 B, int l[], int r[]) {
        for (int i = 1; i <= n; i ++) {
            l[i] = xorshift128p(A, B) % L + X;
            r[i] = xorshift128p(A, B) % L + Y;
            if (l[i] > r[i]) swap(l[i], r[i]);
        }
    }

    struct QAQ {
        int op, h, t, L, R;  // h：头 t：尾   是 [L, R] 的单调队列
        int q[N], a[N];
        void ins(int x) {
            while (h <= t && a[x] <= a[q[t]]) --t;
            q[++t] = x;
        }
        int query(int l, int r) {
            int ans = inf;
            rep(i, l, L - 1) chkmin(ans, a[i]);
            while (R < r) ins(++R);
            if (L < l) {
                L = l;
                while (h <= t && q[h] < L) ++h;
            }
            ans = min(ans, a[q[h]]);
            return op * ans;
        }
    } Q[4];

    void init() {
        rep(i, 0, 3) Q[i].h = 1, Q[i].t = Q[i].R = Q[i].L = 0;
        rep(i, 1, n) {
            Q[0].a[i] = r[i] - i;
            Q[1].a[i] = r[i] + i;
            Q[2].a[i] = -(l[i] + i);
            Q[3].a[i] = -(l[i] - i);
        }
    }

    bool chk(int i, int ans) {
        if (i + ans > n || i - ans < 1) return 0;
        int r = min(Q[0].query(i - ans, i) + i, Q[1].query(i, i + ans) - i);
        int l = max(Q[2].query(i - ans, i) - i, Q[3].query(i, i + ans) + i);
        return r - l >= 2 * ans;
    }

    int main() {
        cin >> T;
        pw3[0] = 1;
        rep(i, 1, N - 5) pw3[i] = mul(pw3[i - 1], 3);
        Q[0].op = Q[1].op = 1;
        Q[2].op = Q[3].op = -1;

        rep(cas, 1, T) {
            scanf("%d%d%d%d%llu%llu", &n, &L, &X, &Y, &A, &B);
            gen(n, L, X, Y, A, B, l, r);
            init();
            ll ans = 0, sum = 1;
            rep(i, 2, n) {
                if (!chk(i, ans)) --ans;
                else if (chk(i, ans + 1)) ++ans;
                Add(sum, mul(pw3[i - 1], ans + 1));
            }
            printf("Case #%d: %lld\n", cas, sum);
        }
        return 0;
    }
    ```
</details>

### [集合划分计数](https://loj.ac/p/154)

写在「集合幂级数」。*还咕着呢！*

## $\mathcal{2.25}$
---

### $XJOI1669$

#### $T1$

尽量卡满的取均值。dp，$dp_{i, j}$ 表示最后一段取均值的是 $[i, j]$，转移考虑下一段取均值的是 $[j + 2, k]$。怎么优化成 $n^2logn$？好像按 $j$ 排序，线段树维护所有左端点的答案吧……

#### $T2$

组合意义天地灭！可以看作一维的平面游走问题，三种分别对应向左、不动和向右，但是概率很鬼：向左、向右各是 $1/4$，不动 $1/2$，考虑把它视为向左、向右均以 $1/2$ 的概率移动 $0.5$，这样移回原来位置的概率仍是 $1/2$。可以列出方程：$L + R = 2n$, $R = L + 2K$。$ans = \frac{ \binom{2n}{n + k} }{2^{n - k}}$

#### $T3$

完全不懂

### [CF850E](https://www.luogu.com.cn/problem/CF850E)

考虑序列和优先顺序的对应性质，一种优先顺序对应唯一一种序列但反之不亦然——考虑一个人赢了两场，使得他赢了另外两人的序列设为 $a$ 和 $b$，如果某一个二进制位 $a$ 和 $b$ 不同那么这位的选民的顺序固定，否则有两种顺序。那么我们实际上要算出每个 $a \oplus b$ 的个数，FWT 即可。最后答案要乘以 $3$。 [$Code$](https://codeforces.com/contest/850/submission/108445337)


## $\mathcal{2.26}$
---

## $\mathcal{2.27}$
---

### [论战捆竹竿](https://uoj.ac/problem/172)

首先要知道性质：将 border 集合中的所有元素排序，最少可以划分成的等差数列个数不超过 $log|S|$ 个。对每个等差数列依次更新。假设这个等差数列形如 $x$, $x + d$, $x + 2 * d$... $x + (l - 1) * d$，我们以 $x$ 为模数跑同余最短路。这个是有次数限制的，用单调队列维护最近 $l$ 个的最小值即可。形成了 $gcd(x, d)$ 个环，每个环单独做。

还有一个难点就是转换模数。$dis_i$ 显然可以更新 $dis_{dis_i \% now}$，但是由于原先的 $dis_i$ 表示 $dis_{i + k * lst}$，还要用 $dis_i$ 更新 $dis_{(i + lst) \% now}$。这是没有长度限制的，可以直接做。

[$Code$](https://uoj.ac/submission/457731) ~~Extra Test 鲨人啦~~

### [$CF1103E-Radix\ sum$](https://www.luogu.com.cn/problem/CF1103E)

裸的高维卷积，模数……不会做

[Orz](https://www.cnblogs.com/Mr-Spade/p/10390667.html#4821784)

考虑 DFT 和 IDFT 我们要干啥。

1. 这显然是个高维 DFT，每维度我们要带 $10$ 个值进去，即单位根 $\omega_{10}$
2. IDFT 的时候要计算序列长度，我们全当它 $10^5$，在模 $2^{58}$ 意义下的逆元

考虑第二问，首先 $5$ 在模 $2^{58}$ 意义下有逆元因为 $gcd(5, 2^{58}) = 1$。所以只要考虑 $2^5$ 的逆元。

神仙处理方法：设逆元为 $x$，$x * 2^5 \% 2^{64}$ 的结果设其为 $y$，那么 $\frac{y}{2^5} \equiv x \pmod{2^{59}}$，再取个模就得到了 $x$。

考虑第一问，$\omega_{10}^5$ 在模 $2^{64}$ 意义下存在，值为 $-1$。$\omega_{10}^2 = \omega_5$，因此 $\omega_5^5 - 1 = 0$。

设 $x = \omega_5$，将 $\omega^5 - 1$ 视为形式幂级数，考虑把计算放在模 $x^5 - 1$ 下进行，因为 $x^5 - 1$ 倍数仍为 $0$，不仅不会影响答案，还能将次数控制在 $5$ 内。但是我们没法保证得到的答案形式幂级数 $a_1 = a_2 = a_3 = 0$。

考虑把模多项式变为 $\frac{x^5 - 1}{x - 1} = 1 + x + x^2 + x^3 + x^4$，其根只有 $\omega_5$ 的 $1$ ~ $4$ 次方，这是可以证明 $a_1 = a_2 = a_3 = 0$ 的！

[$Code$](http://codeforces.com/contest/1103/submission/108591036)

## $\mathcal{2.28}$
---

### [$UER \#9$](https://uoj.ac/contest/61)

### 赶路

第 $5$ 个点启发我们：起点不在凸包上，直接以起点为原点极角排序。起点在凸包上呢？考虑删点 + 绕圈走法，每次走掉最外面一层点，遇到终点就跳过。

还有一种做法是直接分治，每次规定起点、终点和法线（雾），把终点所在的那一半后走，也是 $O(Tn^2)$

[$Code$](https://uoj.ac/submission/459213)

### 知识网络

对每种标签建点，知识点向标签连 $1$ 边，标签向知识点连 $0$ 边。考虑从每种标签开始跑最短路（bfs），计入该标签起点知识点和其他标签知识点的贡献。我们称「其他标签知识点」为「目标节点」，「起点知识点」为「正在计算的知识点」。这对的贡献是最短路长度 - [标签节点到目标节点的最短路是否途经正在计算的知识点]。

那么只要计算每个起点知识点的后继即可。bitset？得手写，否则复杂度就是错的！因为不定长。考虑把起点知识点每 $64$ 个分一组，不用 bitset 啦 unsigned long long 就够了，复杂度 $O(K(n + m) + \frac{n(n + m)}{64})$

注意用 __builtin_popcountll() = =

[$Code$](https://uoj.ac/submission/459338)

### 面试

无语了。。有绝对值在啊，$n^3$ 次询问，哪次为 $0$，$a$、$b$、$c$ 就是哪组啊。这都想不到嘛。

题解看[官方](https://skip2004.blog.uoj.ac/blog/6627)的吧。。重复一遍没啥意思。神啊！要怎样~~闲的蛋疼~~的大脑才想得到这种非人的玩意儿。

[$Code$](https://uoj.ac/submission/459438)

### vjudge 比赛

[链接](https://vjudge.net/contest/424919#overview)

### $B$

是树和基环树啊= = 树只有根是 $-1$。统计连通块数 = 统计环的个数。

基环树的环的贡献一直都在，$(n - 1)^K$。

怎么统计树连成的那个环？就一个跟一个屁股后头！~~（这样形象= =）~~ 所以 $m$ 棵树拼起来的方案数是 $(m - 1)! \prod a_i$，注意一棵树是 $a_1 - 1$。还要乘上其他树随便连的方案数 $(n - 1)^{K - m}$。dp $\prod a_i$ 即可。

### $C$

$S_i = T_i$ 的位，去掉那些不符合的数；$S_i \neq T_i$ 的位，即这一位选了不同的数，需要容斥。设 $f_i$ 表示恰好 $i$ 位不同，$f_t = \sum\limits_{i = 0}^{t} (-1)^{t - i} \sum\limits_{cnt(S) = t - i} g_S$，其中 $g_S$ 表示以 $S$ 为掩码，强制选 $1$ ~ $K$ 个相同的数的方案数，这里是至多 $i$ 位不同的意思。即 $f_t = \sum\limits_{S = 0}^{2^t - 1} g_S (-1)^{cnt(S)}$

### $D$

容斥，不断的容斥。。。插板，不断的插板。。。。就做完了（

考虑容斥，减去 $m \neq m + 1$ 的方案数。设第 $m$ 个为 $x$，前 $m$ 个 $\geq x$，后 $n - m$ 个 $< x$。$\geq x$ 是好做的，减去 $m * x$ 再插板；$< x$ 考虑容斥，枚举后 $n - m$ 个 $\geq x$ 的个数 $q$（这个枚举复杂度是调和级数）减去 $q * x$ 插板。

### $E$

由于是凸包，所以只要确定每个向量取了几次。

设第 $i$ 个向量取了 $c_i$ 次，有如下四个限制：

$$\sum\limits_i c_i x_i = \sum\limits_i c_i y_i = 0$$

$$\sum\limits_{i, x_i > 0} c_i x_i \leq m$$

$$\sum\limits_{i, y_i > 0} c_i y_i \leq m$$

$m$ 很大，需要极端的优化。考虑从低到高 dp 二进制下 $\sum c_i x_i$ 和 $\sum c_i y_i$ 的每一位。非常好的事情是向量很小，仅有 $4$，单这一位不会超过 $4n$。如果我们保留**已选和**的当前位及更高位，这个空间复杂度也是 $4n$，因为 $\sum\limits_{i = 0}^{+\infty} \lfloor \frac{x}{2^i} \rfloor = x$

$dp_{lg, i, j, k, l, op1, op2}$ 分别表示当前第几位、保留「已选正 $x$ 和」的当前位及更高位、保留「已选正 $y$ 和」的当前位及更高位、保留「已选负 $x$ 和」的当前位及更高位、保留「已选负 $y$ 和」的当前位及更高位、$x$ 的后缀和是否大于 $m$ 的对应后缀和、$y$ 的后缀和是否大于 $m$ 的对应后缀和。