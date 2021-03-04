---
title: '21Mar 训练日志'
date: 2021-03-31 23:59:59
tags: 
mathjax: true
encrypt: true
---

高中过去 1/6 啦！

## $\mathcal{3.1}$
---

### [$LG5577-$算力训练](https://www.luogu.com.cn/problem/P5577)

求 $\prod (1 + x^{a_i})$。直接做复杂度感人。考虑带单位根进去。那么 $\prod (1 + x^{a_i})$ 构成的数在集合 $\{ \omega_k^i + 1 \mid i \in [0, k) \cap Z \}$。集合很小，启发我们考虑每一种 $1 + \omega_k^i$ 的出现次数然后累乘起来。



代码咕了

### vjudge比赛

[链接](https://vjudge.net/contest/424925#overview) $B$ 写过

### $A$

套路题，把路线变成斜率为 $\pm 1$ 的直线，它经过横线/竖线的次数就是它横/竖方向改变的次数。算出最终方向，得出终点。~~然而 WA 爆了~~ 注意点是要取最小解！

### $C$

被打爆了 /kk 首先要读清题！~~我tm题目读错了可还行~~ 是问，有多少种数列使得不存在两个位置 $x < y$ 在删除了一些数位后 $a_x > a_y$ 且 $a_x$ 和 $a_y$ 在数位上相差位数 $> 1$。

首先考虑删除者的策略。从高位往低位考虑。设当前数位，第 $i$ 个数字值为 $a_i$。

1. 若 $a_x = a_y$，则当前位对 $(x, y)$ 无影响
2. 若 $a_x < a_y$，最坏情况下当然是删了这位
3. 若 $a_x > a_y$，则要「删不删都合法」，即 $x$、$y$ 更低位的数必须完全相同

dp，每次加一位。设 $f_{i, j}$ 表示 $n = i$, $m = j$ 的答案。若加的是 0000...1111，则直接从 $f_{n - 1, m}$ 转移；否则形如 00001xxx..xx01111，枚举 $x$ 的个数 $c$，则可由 $f_{n - 1, m - (c + 1)}$ 推得。前缀和优化至 $O(nm)$。

### $D$

先考虑没有 '?' 的情况。

考虑一个类 gcd 的过程：去掉开头相同的部分；剩下是 $A$/$B$ 分别开头的情况，一定有一个是另一个的前缀，假设 $A$ 是 $B$ 的前缀，将 $B$ 表示为 $A + C$。一直重复这个过程，容易证明 $(A, B)$ 合法当且仅当 $A$、$B$ 存在一个长度为 $gcd(|A|, |B|)$ 的公共循环节。那么只需要确定 $|A|$、$|B|$。

设 $cnt(s, a)$ 表示 $s$ 串中 $a$ 的出现次数。则 $cnt(s, A)|A| + cnt(s, B)|B| = cnt(t, A)|A| + cnt(t, B)|B|$，$(cnt(s, A) - cnt(t, A))|A| = (cnt(t, B) - cnt(s, B))|B|$

设 $a = cnt(s, A) - cnt(t, A)$, $b = cnt(t, B) - cnt(s, B)$

$a|A| = b|B|$。

- $a = b = 0$：莫反出解, $\sum\limits_{i = 1}^n \sum\limits_{j = 1}^n 2^{gcd(i, j)} = \sum\limits_{d = 1}^n 2^d \sum\limits_{d \mid T} (\frac{n}{T})^2 \mu(\frac{T}{d})$
- $ab \geq 0$：无解！无解！无解！
- $ab < 0$：取绝对值不影响结果。$gcd(|A|, |B|) = \frac{|A|}{\frac{b}{gcd(a, b)}} = \frac{|B|}{\frac{a}{gcd(a, b)}}$，即 $gcd(|A|, |B|)$ 最大可以取到 $\frac{n}{ \max( \frac{a}{gcd(a, b)}, \frac{b}{gcd(a, b)} ) }$，$1$ 到这个的每个数 都能作为 $gcd$ 被取到，所以答案就是 $1$ 到这个的 $2^i$ 的和。

有 '?' 的情况就是 $a$、$b$ 不确定了咯。设 $s$ 串有 $P$ 个 '?'，$t$ 串有 $Q$ 个 '?'，$f(a, b)$ 为 $(a, b)$ 的答案，则

$$ans = \sum\limits_{x = 0}^P \sum\limits_{y = 0}^Q f(a' + x - y, b' + P - Q - (x - y)) \binom{P}{x} \binom{Q}{y}$$

考虑枚举 $x - y$：

$$= \sum\limits_k f(a' + k, b' + P - Q - k) \sum\limits_x \binom{P}{x} \binom{Q}{x - k}$$

后者是 vandemonde 卷积形式，$= \sum_x \binom{P}{x} \binom{Q}{Q - x + k} = \binom{P + Q}{Q + k}$

**要特判两个串啥都相等的情况，因为之前把它当作 $a = b = 0$ 的情况算了，实际上它的答案是 $(\sum\limits_{i = 1}^n 2^i)^2$**

### $E$

从小往大，multiset 维护非空连续段，在 multiset 中最大值 = 最小值的时候更新答案。

## $\mathcal{3.2}$
---

### $B$

~~需要找一个 $0$ ~ $K - 1$ 都和 $K - 1$ 互质的 $K$。我与正解就差一个 $K$！！！心痛~~

根据 $K(K - 1) < n$ 可以算出 $K$ 的范围大概是 $[30, 40]$。第一行 $1$ ~ $K$，接下来 $K - 1$ 行第一位填 $1$，再 $K - 1$ 行第一位填 $2$…… 以此类推。考虑每个 $(K - 1) * (K - 1)$ 的正方形怎么填，第 $i$ 个正方形步长为 $i - 1$，这样从 $K + 1$ 出发就有 $K - 1$ 条不相交的链。容易证明每个正方形里的对和不同正方形里的对都满足限制。

好可惜好可惜好可惜，其实最后 $K = 5$ 的时候 checker 报错我执着的认为是做法错了，就已经心态崩了。。。仔细看看就知道是质数的问题了。可惜。

### $C$

枚举水量转化关系形成的连通块，显然对每个块分别考虑，最后来个 $3^n$ 的子集卷积

- 结论：设某连通块大小为 $k$，点权和为 $A$，边权和为 $B$，则答案为 $\frac{A - B}{k}$

- 一条边只走一次，点权大的点溢出，不断溢出吸收溢出吸收…… ~~证明不存在的，感性理解txdy！~~

要让边权和最小，就找 MST。复杂度 $O(2^n n^2log(n^2) + 3^n)$。

### $D$

考虑一定是先完全的割了一段区间的竖线或横线。接下来考虑先切竖线，横线类似。

假设开始切了 $(x, y)$ 区间的竖线，$f_i$ 表示切了 $i$ 及左边的所有线，$g_i$ 表示切了 $i$ 及右边的所有线，pre_i 表示 $[1, i]$ 上下都有人的线数，贡献就是 $2^{pre_{y - 1} - pre_x} * f_x * g_y = (f_x / 2^{pre_x}) * (g_y * 2^{pre_{y - 1}})$，加上前缀和优化。

考虑 $f_i$ 怎么求（$g_i$ 类似），设 $i$ 及左边部分，左边线数为 $x$，上面线数为 $y$，下面线数为 $z$，贡献是 $\binom{x + y + z - 1}{x - 1}$ 因为第一次必须切横线。

### $E$

完全没看懂。。。先咕了 [题解](https://zybuluo.com/Cyani/note/1779580)

## $\mathcal{3.3}$
---

[比赛链接](https://vjudge.net/contest/425291#overview)

### $A$

看成许多个山峰，盲猜一顿结论。

### $B$

吃饭的时候突然想到做法 把饭倒掉一路狂奔回来赶着最后 20s 交了然后 WA 了嘤嘤嘤——我和正解只差一个特判。。。我特判了没有 $0$ 的情况，但是只适用于 $n > 1$。。。

看成选一个位置、再选其一个相邻位置删掉的方案数。这么看是为了把 $\&$ 和 $|$ 拆成两种，且对于 11、00 组合显然适应，而对于 01、10 组合显然可以看作：先选 1 再选 0 为 $|$，先选 0 再选 1 为 $\&$。于是 dp 即可，$dp_0 = dp_1 = dp_2 = 1$, $dp_3 = 3$（手玩即可），$dp_i = dp_{i - 1} * (2 * (i - 3) + 3)$。最后枚举没删的 $1$，设其在位置 $p$，贡献是 $dp_p * dp_{n - p + 1} * \binom{n - 1}{p - 1}$。

upd：xza 更清晰的解释：$\{x, y\}$ 可以看成 $\{x | y, x \& y\}$。

### $C$

猎人杀本杀。根据期望的线性性，$B_i$ 在所有 $A_j$ 之前被删除的概率很好求，就是 $\frac{B_i}{B_i + \sum A_j}$。对于每个 $B_i$，要求其在所有 $A_j$ 之后删除的概率然后减掉。考虑容斥，枚举 $A$ 的子集 $S$，强制 $B_i$ 在 $S$ 之前被删。发现只和 $\sum A_j$ 有关，dp 即可，$dp_i$ 表示 $\sum A_j = i$ 的容斥系数之和。~~甚至都不用 TNT~~

### $D$

转置原理做多点求值的科技：[rqy讲得好赞！](https://rqy.moe/Algorithms/polynomial-evaluation/)

就是用求 $V^T a$ 的算法求 $Va$。

先考虑怎么用转置原理做简单的多项式乘法。把转置分解为初等矩阵乘积，从 $x += y * c \rightarrow y += x * c$ 理解为什么 $ans_{i + j} += a_i * b_j \rightarrow ans_i += a_{i + j} * b_j$，然后一遍减法卷积。

再来考虑如何做多点求值。考虑求 $V^T a$ 的算法：要计算 $\sum\limits_{j = 0}^{n - 1} \frac{u_j}{1 - \alpha_jx}$（$u$ 是输入的）。通分变成 $\frac{ \sum\limits_{i = 0}^{n - 1} u_i \prod\limits_{j \neq i} (1 - \alpha_jx) }{ (1 - \alpha_1x) \cdots (1 - \alpha_{n - 1}x) }$。

设分母多项式为 $g$，$g$ 可以分治 FFT。分子 $\prod$ 里的东西看作 $\frac{g}{1 - \alpha_ix}$。分子也是分治 FFT 求，每次返回 $ans_L g_R + ans_R g_L$。最后乘上 $g^{-1}$。

转回来，相当于把整个分治 NTT 反过来（当然分治计算 $g$ 是不变的因为它不属于转置部分啊啊）。对于输入的 $u$，先乘上 $g^{-1}$，再魔改分治 FFT 变成先计算 $ans_L = ans[保留一半] * g_R$, $ans_R = ans[保留 len - 一半] * g_L$ 然后分别把 $ans_L$ 和 $ans_R$ 向左向右递归下去。

此处卷积都是减法卷积，只需要后面一半，因此溢出到前一半也没有关系。只需要一次多项式求逆，常数比较小，据说可以跑 5e5。

~~代码不可能有的~~

### $E$

写在「LGV」。

---

Gym 入坑指南：（勿删！有 xza 课件外强推场次）

可看星级

ICPC：
- EC final 18(jiry_2)、19(xudyh)年
- 日韩赛区的 ICPC
- 欧洲的 NEERC、NERC、CERC(15~17)
官网找题解

official international personal contest:
- yandex.algorithm
    2018 final round
    以及一些 qualification round
- russian code cup
- facebook hacker camp
- google code jam
    final

school/university/city/region championship
- CCPC
    分站赛
    final(18、19)
- 多校

opencup contest & training camp contest
- moscow workshop
    https://discover.it-edu.com/en/
- petrozavodsk camp 毛营题
    有一些科技
- open cup
题解找google

看讨论？也许有题解

opencup 里推荐的
- XXI opencup gp of tokyo
- XX opencup gp of tokyo
- XXI korea
- 2019 ICPC asia east continent final
- 300iq contest 1 ~ 3
- rng_58 (14、15) 毛营
---

## 今天的计划应该是——

订正昨天模拟赛

清华集训

loj6295（线性规划）

uoj448

生成函数：LOJ3102 luogu5577 数树

集训队作业：化学竞赛

写 exgcd 总结（通解）

写 polya 学习笔记