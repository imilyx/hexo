---
title: 'CF 快乐板刷之路'
date: 2021-07-09 18:11:40
tags:
mathjax: true
---

### [$CF1396E-Distance\ Matching$](https://www.luogu.com.cn/problem/CF1396E)

这种极易停止撕烤的题先考虑有无解。即求答案范围。

$dist(x, y) = dis_x + dis_y - 2 dis_{lca(x, y)}$

最小化 lca $dis$ 之和，尽量在浅的点配完

！根据某经典模型，选重心可以配完并且 lca 都是重心。同理求得 $minans$。

$$maxans = \sum dis_i = \sum [i \neq root] sz_i$$

$$minans = \sum [i \neq root] (sz_i \mod 2)$$

$maxans - k$ 显然必须是 $2$ 的倍数。否则设 $rest = (maxans - k) / 2$，显然可以通过不断选大小最大的子树的一对点配对使得 $rest$ 变小。~~模拟？😅~~

可以将某些非叶子节点变成 lca。具体来说：

- $rest \geq$ 大小最大的子树深度 $d$ 时，选该子树最深的那对兄弟或父亲，$rest -= d - 1$ (I)
- 否则令深度 $rest$ 的点为 lca (II)

细节：
- set 维护重心所有子树的大小和 id，方便找最大子树
- 对每个子树开一个 set 维护子树中点的深度和 id，方便找最深点
- 只有一次是 II 情况，其余都是 I，I 显然每次删的都是两个叶子的父亲或一个叶子和它的父亲，所以点匹配掉要记得**将父亲度数减一**（还要从父亲的叶子集合里删掉，但是想要快速删除就要多个 log，还是懒惰删除更香一点，即遇见时删除），父亲成为叶子就把父亲从备选深度集合里踢掉。

[$Code$](https://codeforces.com/problemset/submission/1396/122195667)

### [$CF1322E-Median\ Mountain\ Range$](https://www.luogu.com.cn/problem/CF1322E)

中位数问题，想到「01 序列」基本这题就只剩代码难度了。

我们把长度 $> 1$ 的 0 和 1 称为 0 段和 1 段，0、1 交错出现的称为 01 段。
显然 0 段和 1 段是稳定的，
01 交错出现部分每次操作会两边各缩进去一个单位。

拓展，$\leq x$ 为 1，$> x$ 为 0。某位取值为 $a$，当且仅当 $x = a$ 时该位置的终态改变。

从小到大枚举 $x$。操作次数 $=$ 所有 $x$ 的操作次数 $max$，因为实际操作序列肯定存在最后一步，当 $x =$ 最后一步的中位数时取到 $max$。

细节：

每次 0 -> 1 可能会让 0 段和 01 段变为 01 段和 1 段。每次操作只会至多出现一个 01 段需要赋值。

- 并查集维护同色块，set 维护同色**对**的位置（这俩方便确定 01 交错段的端点坐标）
- set 维护 0 的位置，防止对 01 交错段中的 0 重复赋值（赋值指 $ans = x$）。

[$Code$](https://codeforces.com/contest/1322/submission/122324706)

### [$CF1481F-AB\ Tree$](https://www.luogu.com.cn/problem/CF1481F)

考虑枚举答案。设最大深度为 $l$，那么答案下界为 $l$，当且仅当每个深度颜色相同。

背包？物品种类 $O(\sqrt{n})$ 级别，也就是说 $O(n \sqrt{n})$ 的复杂度即可判断答案能否为 $l$。$dp[x, i]$ 表示前 $x$ 种物品填了 $i$ 个 a，bitset + 多重背包

然后呢？

官解告诉我们上界为 $l + 1$。证明见如下构造过程，有点脑筋急转弯的感jio：

将点分为叶子节点和非叶子节点，非叶子节点个数 $\leq \frac{n}{2}$ 个，**每层**非叶子节点必然可以同色，考虑从上往下填色，每层的非叶子节点先填同一种颜色，然后同层的叶子节点尽量填该层非叶子节点的颜色，若填完了就填另一种。

[$Code$](https://codeforces.com/problemset/submission/1481/122535450)

### [$CF827F-Dirty\ Arkady's\ Kitchen$](https://www.luogu.com.cn/problem/CF827F)

策略是反复横跳。设「最小时刻」的 dp 状态是不对的，因为如果来的边 ban 掉，又没有邻边存在，就会挂。

按出现时间从小到大加边，堆维护，$f[x, 0/1]$ 表示在偶/奇时刻到达 $x$ 点的最大时刻，那么加入的边出现时间只要 $\leq f[x, 0/1]$ 就可以拓展，否则 $> f[x, 0/1]$ 就先放在 $x$ 的 vector 里，等到什么时候 $f[x, 0/1]$ 更新了才拓展（更新 $f[x, 0/1]$ 一定是之后的边的 $r$，一定大于当前边的 $l$，所以一定可以拓展）。因为边有序，vector 也有序。显然你不用考虑删边了。

这样一条边最多被考虑两次，复杂度 $O(nlogn)$。

[$Code$](https://codeforces.com/problemset/submission/827/122544419)

### [$CF1550D-Excellent\ Arrays$](https://codeforces.com/contest/1550/problem/D)

卡点很奇怪的一道题（？

设 $k_i = a_i - i$，那么 $k_i = -k_j$ 的对贡献 $1$，也就是说按 $|k_i|$ 划分，每个部分内部贡献的和最大

~~然后我到这儿就卡住了~~

显然可以划分为一个集合，其中一半 $+k$，一半 $-k$，因此 $F(a) = (n/2)^2$

不同 $k$ 的贡献计算方式不同。

- $k \leq min(1 - l, r - n)$：偶长度序列答案为 $\binom{n}{n/2}$，奇长度序列答案为 $\binom{n}{n/2} + \binom{n}{(n/2) + 1}$，就考虑选了哪些为 $+k$

- $k > min(1 - l, r - n)$：设 $lf = max(1, l + k) + 1$, $rg = min(n, r - k) - 1$
  $i \in [1, lf)$ 只能选 $+k$, $i \in (rg, n]$ 只能选 $-k$。稍微推一下得到，偶长度序列答案为 $\binom{rg - lf + 1}{(n/2) - lf + 1}$, 奇长度序列答案为 $\binom{rg - lf + 1}{(n/2) - lf + 1} + \binom{rg - lf + 1}{(n/2) + 1 - lf + 1}$
  
  这部分复杂度是合法 $k$ 的个数，是多少呢？$k$ 每 $+1$，$lf++$、$rg--$，直到 $rg - lf + 1 \leq 0$，一共是 $O(n)$ 个 $k$。

[$Code$](https://codeforces.com/contest/1550/submission/122626451)

### [$CF1550E-Stringforces$](https://codeforces.com/contest/1550/problem/E)

满足二分性。check 考虑贪心的把大小为 $lim$（二分值）的块尽量往左放，看能否 fit

每种字符都要出现，于是状压 dp 加块顺序。预处理每个位置往后每种字符最近的长度合法的连续段的结束位置。
若当前位置 $i$ 可以放某种颜色的块，当且仅当其他所有颜色上一次出现位置都 $\geq i + lim$

[$Code$](https://codeforces.com/contest/1550/submission/122627687)

### [$CF1550F-Jumping\ Around$](https://codeforces.com/contest/1550/problem/F)

喵题（但似乎没有很大的难点）

首先肯定不能每次判断。由于能跳到每个位置的最小 $k$ 具有单调性，考虑预处理每个位置被到达的最小 $k$。

考虑两两连边，边权 $w$ 为互相到达的最小 $k$，显然 $w = | d - | a_u - a_v | |$

那么两个点可互达的最小 $k$ 就可以用 kruskal 重构树算，其实就是建 MST 啦

怎么建 MST 啊？这是完全图！kruskal 暴毙，prim $n^2$，而 boruvka 可以，因为这里距离的计算方式比较特殊

用 set 维护所有点的坐标。处理当前块时，将 set 中当前块的坐标清出去，这样复杂度才对。$O(nlog^2n)$。

[$Code$](https://codeforces.com/contest/1550/submission/122630068)

### [$CF283E$](https://www.luogu.com.cn/problem/CF283E)

不合法的三元组一定是存在一头牛可以打败另外两头

$d_i$ 表示第 $i$ 头牛能打败 $d_i$ 头牛

$ans = \binom{n}{3} - \sum\limits_{i = 1}^n \binom{d_i}{2}$

$d_i$ 的区间一定包含 $i$，因此将区间按左右端点升序排列，依次反转区间（SGT 维护 tag），扫到 $i$ 为左端点时统计 $[i + 1, n]$ 中被反转了奇数次的和 $[1, i - 1]$ 中被反转了偶数次的。

### [$CF1400F$](https://codeforces.com/problemset/problem/1400/F)

盲猜 x-prime 区间很少，跑一跑

~~咦为什么我 num = 19 只有 870？题解说 2400~~

诶不管了，总之就是 dp, $dp[i, j]$ 表示考虑前 $i$ 个字符在 AC 自动机状态 $j$ 的最少删除个数

[$Code$](https://codeforces.com/problemset/submission/1400/123013668)

### [$CF1528D$](https://www.luogu.com.cn/problem/CF1528D)

形式化：$u \rightarrow v$，等一秒就变成 $u \rightarrow v + 1$，其实可以看作 $v \rightarrow v + 1$。连边跑 dij 即可。

注意 dij 扩展到的点，最短路已经确定，要根据最短路的 dis 确定当前边**真正**的终点。

细节：由于是个边数 $n^2$ 级别的稠密图，dij 不用堆会更快哦。

[$Code$](https://codeforces.com/problemset/submission/1528/123016914)

### [$CF587F$](https://www.luogu.com.cn/problem/CF587F)

[$CF547E$](https://www.luogu.com.cn/problem/CF547E) 的孪生姊妹。

哦说到那题，一种做法是离线询问、建立 AC 自动机并用 BIT 维护 fail 树，另一种做法是直接把串拼起来，就变成询问一个区间内某串出现次数的 SAM + 线段树合并板子题。

再来看这题，似乎直接思考没有靠谱做法？

暴力做法是在 ACAM 上跑 $s_k$，统计 fail 树上到根路径上是 $s_{l \cdots r}$ 的结尾的点的个数。

考虑根号分治：
- 长度 $< \sqrt{n}$ 的用暴力做法。
- 长度 $> \sqrt{n}$ 的只有 $\sqrt{n}$ 个串，可以预处理每个串在它里面出现的次数。

### [$CF603E$](https://www.luogu.com.cn/problem/CF603E)

假设你已经知道了判定方法，然后来个删边并查集 + cdq 分治即可。

现在问题是，判定方法/充要条件是啥？**每个连通块点数为奇**。

考虑加入一条边会使得奇度点个数 $+2$，不变或 $-2$，必要条件是每个连通块点数都为偶。

充分性考虑构造一个偶数个点的连通块的合法边集：选个生成树，从下往上删即可！！

于是你就可以将边升序排序后 cdq 啦！

当前时间 $[l, r]$，边权 $[x, y]$

假设当前已经加入了时间在 $[1, l)$ 且边权在 $[1, x)$ 的所有边

只要求出 $ans_{mid}$ 就可以递归到 $(l, mid - 1, ans_{mid}, y)$ 和 $(mid + 1, r, x, ans_{mid})$

先将 $[l, mid]$ 中边权 $[1, x)$ 的所有边加入，然后按边权从小往大加到所有连通块点数都为偶为止。

$O(mlogmlogn)$，每条边被加入删除它的深度次，$logn$ 是删边并查集的复杂度。

---

# *可能要暂时断更了，没有显著成效，noi 同步赛打得稀烂*

---