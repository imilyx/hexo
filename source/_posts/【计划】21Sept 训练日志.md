---
title: '21Sept 训练日志'
date: 2021-09-30 23:59:59
tags: 
mathjax: true
encrypt: true
---

## $\mathcal{9.1}$
---

### [纯洁憧憬](https://www.luogu.com.cn/problem/P7278)

析合树板题，不表。

### [平凡点滴](https://www.luogu.com.cn/problem/P7278)

写在「数论」。

### [春天，在积雪下结一成形，抽枝发芽](https://loj.ac/p/3397)

挑战失败

### [大鱼洽水](https://loj.ac/p/3391)

挑战失败

### [$\text{Equalization}$](https://www.luogu.com.cn/problem/P7718)

区间加，差分后得到数组 $d$，去掉 $= 0$ 的位置，变为单点操作。具体来说，每次可以给单点加/减一个数，也可以给一对点一个加一个减一个数

考虑对所有「成对操作」的点之间连边，对于每个连通块 $(V, E)$ ，设 $S = \sum\limits_{x \in V} d_x$

若 $S = 0$，则操作形如一棵树；否则形如一棵树加一个自环。

这样就能做第一问了！状压即可。枚举子集，$3^{n - 1}$。

考虑第二问。代表操作顺序的阶乘，放到最后乘。

还是看 $S$。

- $S = 0$ ：$|V|^{|V| - 2}$
- $S \neq 0$ ：钦定一个位置 $x$ 放自环，注意有两种选择：$[1, x]$ 和 $[x + 1, n]$，因此为 $2 |V|^{|V| - 1}$

### [$\text{NWRRC2015-Graph}$](https://www.luogu.com.cn/problem/P7054)

首先若最终拓扑序为 $p$，则存在一种方案满足加边都形如 $p_i \to p_{i + 1}$。

因此被认证「应当有新增边指向它们」的点，并不用急着钦定前驱。

而拓扑序某个位置的可选点集合实际上只有当前度数为 $0$ 的点集 $(\text{I})$ 以及部分被新增边指向且未钦定前驱的点集 $(\text{II})$。

$\text{II}$ 可以通过前驱后继关系来限制只能选最大点，$\text{I}$ 在最坏情况下选最小点，当前最坏情况取的实际上是 $\min( \text{I}_{min}, \text{II}_{max} )$，因此在 $\text{I}_{min} < \text{II}_{max}$ 时要尽量让 $\text{I}_{min}$ 变大，即使用一次 $K$ 把 $\text{I}_{min}$ 踢到 $\text{II}$ 里。

实现：可以用小根堆维护 $\text{I}$，大根堆维护 $\text{II}$。注意 $\text{I}$ 为空、$\text{II}$ 为空、$K = 0$、$\text{I}_{min} > \text{II}_{max}$ 等边界情况。

## $\mathcal{9.2}$
---

### [清训$\text{-Hello World!}$](https://uoj.ac/submission/506296)

按操作执行。行走方式：若 $s$ 到 $t$ 的距离 $\leq K$ 就会直接跳到 $t$，否则沿着这条路径**一次性**跳 $k$ 条边（中间的 $k - 2$ 个点不修改不询问）

1. 削弱：将走到的点的 $a_i$ 开根
2. 询问：询问走到点的 $a_i$ 之和

$10^{13}$ 的数开 $6$ 次根一定变成 $1$。实际有效操作只有 $6n$ 次。

但是这个走路方式好吊诡啊！考虑根号数据结构

首先 $K > B$ 的时候可以直接暴力跳并修改/询问。

$K < B$ 怎么做啊？

- 修改：我们显然只想要那些有效操作，可以利用树上并查集找到当前点向上 $k$ 的整数倍第一个 $a_i > 1$ 的位置
- 询问：开 $B$ 棵 BIT/SGT 维护答案，你要询问的是路径，可以差分为询问到根的路径，BIT 经典应用，可视为子树加、单点查。

实测 $B$ 取 $30$ ~ $40$ 最优，但还是被 UOJ 的 Extra test 卡飞了。

### [$\text{WC2019-I}$ 君的商店](https://uoj.ac/problem/459)

$0$ 表示 $\geq$，$1$ 表示 $\leq$

朴素想法：首先花 $2n$ 代价，归并找到一个 $1$
然后考虑每次随机选一对 $(x, y)$，询问 $x + y$ 是否 $\geq 1$，再询问 $x$、$y$ 之间的关系可以确定其中一个。代价 $5n$。总共 $7n$。

考虑链，是不是直接上二分找到第一个 $x + (x + 1) \geq 1$ 的位置就可以了哇。

考虑一边 $5n$ 一边确定一个递增的链状结构。（神仙！）

先询问 $x$、$y$ 之间关系，假设 $x \leq y$
询问 $(z, \left \{x, y\right \})$，$z$ 是当前链尾

若 $x + y \leq z$，则 $x = 0$，并新选一个 $x$

否则 $y \geq z$，可将 $y$ 放到 $z$ 的后面，并新选一个 $y$

这样若干次做到不能做，会确定若干 $0$、一条递增的链，无法确定的有 $x$ 和链的分界点。

根据全局奇偶性，询问常数次即可确定 $x$ 和分界点的取值。

### [$\text{WC2014-}$时空穿梭](https://uoj.ac/problem/54)

枚举每维第一个点和最后一个点的坐标差

以下的 $m_i$ 为输入 $-1$。

$$
\sum\limits_{x_i \leq m_i} \binom{gcd(x_i) - 1}{c - 2} \prod\limits_{i = 1}^n (m_i - x_i) \\
= \sum\limits_{d = 1}^{\min(m_i)} \binom{d - 1}{c - 2} \sum\limits_{x_i \leq \frac{m_i}{d}} [gcd(x_i) = 1] \prod (m_i - d x_i) \\
= \sum\limits_{d = 1}^{\min(m_i)} \binom{d - 1}{c - 2} \sum\limits_{x_i \leq \frac{m_i}{d}} \sum\limits_{p \mid x_i} \mu(p) \prod (m_i - dx_i) \\
= \sum\limits_{d = 1}^{\min(m_i)} \binom{d - 1}{c - 2} \sum\limits_{p = 1}^{\min(\frac{m_i}{d})} \mu(p) \prod\limits_{i = 1}^n \sum\limits_{x_i = 1}^{\lfloor \frac{m_i}{dp} \rfloor} (m_i - dp x_i) \\
= \sum\limits_{d = 1}^{\min(m_i)} \binom{d - 1}{c - 2} \sum\limits_{p = 1}^{\min(\frac{m_i}{d})} \mu(p) \prod\limits_{i = 1}^n ( \lfloor \frac{m_i}{dp} \rfloor m_i - dp \binom{\lfloor \frac{m_i}{dp} \rfloor}{2} ) \\
= \sum\limits_{D = 1}^{\min(m_i)} \prod\limits_{i = 1}^n ( \lfloor \frac{m_i}{D} \rfloor m_i - D \binom{\lfloor \frac{m_i}{D} \rfloor}{2} ) \sum\limits_{d \mid D} \binom{d - 1}{c - 2} \mu(\frac{D}{d})
$$

设右边那坨 $= G(D)$，可以预处理（指在输入 $T$ 前预处理）

这可恶的题目呵，当下我们求 $\prod$ 要做 $10^5 \cdot 20$ 次运算，而 $T = 100$！简直惨绝人寰。要继续优化。咸鱼叹气。

于是不能枚举 $m$ 次 $D$ 了，因为你观察到那个 $\prod$ 是个 $n \sqrt{m}$ 段、次数为 $n$ 的分段函数！于是你枚举段。某下标 $[l, r]$ 段的贡献为 $\sum\limits_{D = l}^r G(D) F_{[l, r]}(D) = \sum\limits_{i = 0}^n coef_i \sum\limits_{D = l}^r G(D) D^i$。（这个 $coef$ 表示 $[l, r]$ 段的系数）

后面这坨可以在输入 $T$ 之前预处理出来存在 $G[i, c, D]$ 里的。

还有一个很恶心的东西是，模数仅有 $10007$，因此大于等于 $10007$ 的东西没有逆元！组合数也要预处理！

复杂度 $O(nmlogm + cnm + T n^3 \sqrt{m})$

[$Code$](https://uoj.ac/submission/506398)

### [$\text{JOI-}$三明治](https://loj.ac/p/2733)

写在「JOI」。

### [$\text{CTSC2016-}$时空旅行](https://uoj.ac/problem/198)

写在「【学习笔记】线段树分裂、分治、二进制分组」。

### 补了 EC Final 2019 C 的证明

---

断更的这几天，做题记录详见「近期数数特训」和「【学习笔记】Min_25 筛」。

---

## $\mathcal{9.7}$
---

干了一道「人类的本质」，ddl 题！！！1

## $\mathcal{9.8}$
---

- 得把昨天那道博客整理了。
- 继续专题复习
- 冲刺：切树游戏！

今日作业：
- 数学：小卷子
- 英语：阅读训练 ABC
- 物理：紫本，卷子
- 化学：卷，蓝本p43

---

xtr 的杭电选讲

---

计算几何

圆并

集训队-圆形

---

字符串：

CF594E cutting the line (zzy题) https://www.luogu.com.cn/problem/CF594E

zjoi2017 字符串 https://www.luogu.com.cn/problem/P5211

zjoi2020 字符串 https://www.luogu.com.cn/problem/P6629

cf653f https://www.luogu.com.cn/problem/CF653F

cf862f https://www.luogu.com.cn/problem/CF862F

cf1110h https://www.luogu.com.cn/problem/CF1110H

loj6681 https://loj.ac/p/6681

ctsc珠宝商 https://www.luogu.com.cn/problem/P4218

---

咕咕咕的：

cf582d https://www.luogu.com.cn/problem/CF582D

sasha circle https://www.luogu.com.cn/problem/CF549E

类 thusc 题 https://www.luogu.com.cn/problem/P7718

小修和小栋生♂成树 https://loj.ac/p/2488

无标号图编码 https://uoj.ac/problem/679

有根无标号「奇树」计数 https://loj.ac/d/1927

---

### 串串

### strings

### joisc 2019 聚会

### 300iq A

### 杨表

### JOISC 2021

### UR 13

### NOI2019

### IOI 18 19 20

### 矩阵变换

### k 小割

### 观众小 P

### CERC2017-Cumulative Code

分讨题。晚点搞它。

### [$LOJ565-$mathematican 的二进制](https://loj.ac/p/565)

### 舌尖上的方伯伯（计算几何）https://loj.ac/p/2216

### 订正 opencup Kore

### 订正 XJOI1730 T3？

试试

### 析合树……

你鸽了多久了？

### USACO 2021

### 300iq contest 3

### 2017-2018 ACM-ICPC, Central Europe Regional Contest (CERC 17) https://codeforces.com/gym/101620

### LCT 维护子树信息

写 exgcd 总结（通解）

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

出题思路：
LOJ6509 上图版
k 进制 FWT 板题，或者稍微变个花样