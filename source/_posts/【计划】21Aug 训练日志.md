---
title: '21Aug 训练日志'
date: 2021-08-31 23:59:59
tags: 
mathjax: true
encrypt: true
---

暑假过去一半叻！

- 预习高中数学

## $\mathcal{8.2}$
---

今天就一道 ROI + 订正 XJ 题吧，听说后面有神仙学长的课——学长赛高！

### [$ROI2016$-人烟之山](https://loj.ac/p/3063)

写在「半平面交转凸包」。

### 订正了 XJOI2759 T2

## $\mathcal{8.3}$
---

### 订正了 XJOI2759 T3

### [$UOJ422$-小 Z 的礼物](https://uoj.ac/problem/422)

显然的 min-max 容斥，$max(E(S)) = \sum\limits_{T \subseteq S} (-1)^{|T| + 1} min(E(T))$

$n * m$ 的方块，选的总方案是 $(n - 1)m + (m - 1)n$

$min(E(T)) = \frac{ (n - 1)m + (m - 1)n }{ 包含任意一个 T 中礼物的相邻格子对数（这里格子对不需要完全位于 T 内部的） }$

$n$ 这么小肯定状压啊，就维护一个状态条，一格格推（低配版插头 dp）$dp[i, 0/1, k, s]$ 表示前 $i$ 个格子考虑完了，选格子数奇偶性，选取格子中礼物的相邻格子对数之和为 $k$，当前选择状态为 $s$ 的方案数

第一维滚掉，第二维这个容斥系数啊乘到方案里。

[$O(n^2m^2 2^n)$](https://uoj.ac/submission/501519)

### 订正了 XJOI2737 T2

### 订正了 NOI2021 D2T2

## $\mathcal{8.4}$
---

今日计划：loj 6684，uoj图编码

### $XJOI2896$

#### $T1$

可能是本场唯一有脑子的题？

等价于求 $x * k^r \equiv x \pmod{m}$ 的最小 $r$，

根据 $ac \equiv bc \pmod{m}$ 等价于 $a \equiv b \pmod{\frac{m}{gcd(c, m)}}$，就是求 $k^r \equiv 1 \pmod{\frac{m}{gcd(x, m)}}$ 的阶，我们称作 $ord(k, \frac{m}{gcd(x, m)})$

设 $gcd(x, m) = g$

分成若干环，$g$ 不同的点所在环大小（即阶）不同

枚举 $g$

就是求这个东西：$\sum\limits_{g | m} \frac{ \sum\limits_{i = 0}^{m - 1} [ gcd(i, m) == g ] }{ ord(k, \frac{m}{g}) } = \sum\limits_{g | m} \frac{ \varphi(\frac{m}{g}) }{ ord(k, \frac{m}{g}) } = \sum\limits_{g | m} \frac{\varphi(g)}{ord(k, g)}$

考虑 dfs 搜所有 $g$，$\varphi(g)$ 是好求的。$ord(k, g)$ 怎么求呢？BSGS 常数大而且烦！实际上可以不用，因为你发现 $ord(k, g) \mid \varphi(g) \mid \varphi(m)$，可以存下所有 $\{k^i \mid i\ is\ a\ divisor\ of\ \varphi(m)\}$，只有 $O(\sqrt{ \varphi(m) })$ 个。$i \leq \sqrt{\varphi(m)}$ 的存在 $bin1[i]$ 里，$i > \sqrt{\varphi(m)}$ 的存在 $bin2[\varphi(m) / i]$ 里。

关于相乘爆 ll 这件事，写个龟速乘完事！

#### $T2$

考虑路径看作点，两条相交异色路径连边，构成二分图，要求二分图带权最大独立集

求解这个显然只能流了！

$S$ 与红路连，绿路与 $T$ 连，权值为价值，相交的红绿路径之间连 $\infty$ 边，这样一个割代表一种选择方案，该方案的实际价值为红绿边权和 - 割，那么最优方案就是边权和 - 最小割。

#### $T3$

考虑这个价值怎么回事，就是块内有向路径数。

有向路径数期望怎么求？$i$ 天后 $path(x -> y)$ 依然健在的概率是 $\frac{ \binom{n - 1 - dist(x, y)}{i} }{ \binom{n - 1}{i} }$

那么点分治 + 卷积求出每种长度有向路径条数即可。注意要容斥减去来自同一个子树的。

现在已经得到了每种长度的有向路径条数。如何得到答案呢？化一下式子就可以卷了~

## $\mathcal{8.5}$
---

听了一早上 + 学了一下午 + 一晚上线代，起飞 ~ 写了学习笔记！

## $\mathcal{8.6}$
---

上午听 zzy ~~大谈~~ 字符串。所以下午和晚上的打算是，字符串专题 + 咕咕咕的计数题

### ZJOI2016-小星星

补一下题解。

就是给节点重标号，在图中也满足树上连边关系。

朴素想法是 $dp[i, j, s]$ 表示 $i$ 编号为 $j$，$i$ 子树编号集合为 $s$ 的方案数。但这样每次拼进来子树都要枚举子集啥的…… $O(n^3 3^n)$ boom！

考虑 $s$ 这维存在的意义就是为了编号不重，换个角度看就是所有编号都被取到恰好一次。那么容斥！枚举集合 $t$ 表示 $t$ 中编号被取到至少一次，做 $2^n$ 次 $n^3$ dp 完事。

### 订正了某模拟赛线代题

### [Mirror Box](https://www.luogu.com.cn/problem/CF578F)

首先感觉是不能有环…… 但这条件好像不太对劲，你是说图要全连通呢还是将外圈视为点后不能有环？

实际上是黑白染色后，一种颜色的点构成树，另一种颜色都是孤立点。

为什么？首先要连通，不然光线会乱跑；其次不能有环，这比较显然。所以就是树。

### [CF605E](https://www.luogu.com.cn/problem/CF605E)

$E_i$ 表示 $i$ 到 $n$ 的期望天数

想象一下，最终的 $E$ 计算出来后递增排列，每个 $E$ 肯定是依赖于之前的点的 $E$，因为只会往期望小的点走嘛。否则就原地等待呗。

设 $u$ 的后继为 $v_i$ 且 $E_{v_i}$ 不降

如果知道了 $v$ 序列就可以轻松算出，但是不行

考虑随便选一个点作为 $v_{i + 1}$，然后扩展。略显无赖的想法是，答案就在那儿，答案是固定的，而下个点的 $E$ 只依赖于前面这些已经计算出来的 $E$，很奇妙但这就是对的。

$E(u) = 1 + \sum_i \frac{E_{v_i}}{1 - \prod\limits_{j = 1}^n (1 - p_{v_i, j})} p_{u, v_i} \prod\limits_{j = 1}^{i - 1} (1 - p_{u, v_j})$

第一个除表示不考虑在 $v_i$ 点等待大于等于一轮的情况，因为那样涉及到未来，也许还不如在 $u$ 等一轮优呢。

啊啊啊啊啊啊啊啊似乎还是问题很大，搞明白呜呜呜呜呜呜呜呜

### [APIO2017-商旅](https://loj.ac/p/2308)

首先二分。

只能持有一个商品。点只有 $nK$ 个，边大概是 $n^2 * K$ 的级别。这其实已经爆炸了

仔细看一下这个交易过程：$i$ 买，$j$ 卖（可以选择买或不买），第一次拥有的商品 $k$ 的贡献就是 $S_{j, k} - B_{i, k}$

你发现在最优化条件下，这个商品是啥也不重要了，我们只要处理出 $mx_{i, j} = \max_k ( S_{j, k} - B_{i, k} )$

所以就连 $(i, j, mx_{i, j} - mid * mindist(i, j))$，然后找最长路（记得判正环）。

## $\mathcal{8.7}$
---

上午 zrf 讲计算几何。再一次深刻的感受到 zrf 的个人魅力（雾），谦逊，严谨求实，精益求精。好想成为这样的人！

## $\mathcal{8.8}$
---

### 写了定向越野

## $\mathcal{8.9}$
---

NOI 同步赛成绩出啦…… 竟然比我的预估和洛谷评分都高，205…… 说实话又高兴又遗憾，差一点就比 myy 高了！

### 勘破神机

## $\mathcal{8.10}$
---

大概只听 + 写了 PE 题？

## $\mathcal{8.11}$
---

口胡了 xza 课件上的题。

不能再这么鸽鸽了！！（

## $\mathcal{8.12}$
---

知道了 Mac 的 vs code 代码格式化命令是 Shift + Option + F

### 订正了 XJOI2741 T1

## $\mathcal{8.13}$
---

### CF1278F

### CF1466H

## $\mathcal{8.14}$
---

暑假下午 5 点开始～ 持续一周！

### 暑假计划

练琴
摸鱼塞壬 yes
囤魔方 yes
做 gym
打 CF
清清单
线代
数学卷子 yes
更高更喵
物理卷子
化学卷子
看论文集
看 hhz 课件

### 8.17

订完 turing T3 下楼走会儿

回来把 hhz 课件看了

拉小提琴

写数学作业 + 可能有的化学卷子

睡觉

---

计算几何

定 板 子 啦

uva12304

平面图

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