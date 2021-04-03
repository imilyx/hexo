---
title: '21Apr 训练日志'
date: 2021-04-30 23:59:59
tags: 
mathjax: true
encrypt: true
---

### 鱼人节快乐！

橙蓝紫红灰橙红——某谷给我的颜色！

### 男人八题

单写。

### Noionline

单写。

## $\mathcal{4.2}$

### $XJOI1724$

#### $T1$

先手第一次操作前，树被划成了若干连通块。

奇数点的连通块一定先手赢：一个点的连通块显然先手；否则后手划一条边把连通块割成一奇一偶，归纳可证。（若 n 为奇数，先手赢）

偶数麻烦。考虑染叶子，这个策略很优：

1. 是链：
    - 长度 $\geq 4$：先手选叶子，后手只能选叶子父亲，转为奇数，先手赢；
    - 长度为 $2$：后手赢
3. 不是链：一定存在一个最低分叉点 $v$ 有 $\geq 2$ 个儿子
    - $v$ 子树中有除 $v$ 以外的非叶节点：若先手染叶子，后手就要染该叶子的父亲，那减少了三个空点，转为奇数，先手赢；
    - $v$ 儿子全是叶子：先手染 $v$，后手无论如何操作都无法一次染色所有 $v$ 的儿子，先手赢。

综上所述，当且仅当 $n$ 为偶数且树存在完美匹配且 $K \geq n / 2 - 1$ 时后手赢。

直观理解，先手选一个叶子 $x$，强迫后手选叶子父亲 $y$ 同时断掉 $y$ 的其他边否则就一次染三个，转为奇数了。但是这个很难直接想到嘛…… 还是乖乖分讨吧。讲真分讨也是挑角度的，我咋就不会做呢…… 我们班一大堆胡出来结论不会证的= = 羡慕了

#### $T2$

放在三维平面上，segment tree beats 裸题；还有更好的做法吗？hhz 来㗅了一嘴：$O(n)$ 做法简单又好写：选一维，这一维方向的立方体从头到尾都顶着，拍成二维台阶状；另外两维你发现就是删一个前缀/后缀。双端队列即可。

#### $T3$

十分清真的生成函数！

$ans = \frac{n!}{K^n} [x^n] ( \sum\limits_{i \geq 0} \frac{i^F x^i}{i!} )^L e^{(K - L)x} x^i$

化下降幂：

$\sum\limits_{i \geq 0} \frac{i^F x^i}{i!} = \sum\limits_{j = 0}^F { F \brace j } x^j e^x$

多项式快速幂算出来这个多项式，设为 $\sum\limits_{i \geq 0} a_i x^i$

$ans = \frac{n!}{K^n} \sum\limits_{i = 0}^n a_i [x^{n - i}] e^{kx}$

$= \frac{n!}{K^n} \sum\limits_{i = 0}^n a_i \frac{K^{n - i}}{(n - i)!}$

$= \frac{1}{K^n} \sum\limits_{i = 0}^n a_i \frac{K^{n - i}}{n^{\underline{i}}}$

保证了 $FL \leq 5e4$，直接做就是对的。

### [$CF623E$（缺代码）](https://www.luogu.com.cn/problem/CF263E)

$dp_{i, j} = \sum\limits_{k = 1}^j dp_{i - 1, j - k} \binom{j}{k} 2^{j - k}$

EGF：$dp_i(x) = dp_{i - 1}(2x) (e^x - 1)$

归纳：$dp_1(x) = e^x - 1$，$dp_2(x) = (e^x - 1)(e^{2x} - 1)$ $\cdots$ $dp_n(x) = \prod\limits_{i = 0}^{n - 1} (e^{2^ix} - 1) = \sum\limits_{i = 0}^{2^n - 1} (-1)^{cnt(i)} e^{ix}$

$ans = [x^k] e^x dp_n(x) = [x^k] \sum\limits_{i = 0}^{2^n - 1} (-1)^{cnt(i)} e^{(i + 1)x} = \sum\limits_{i = 0}^{n - 1} (-1)^{cnt(i)} (i + 1)^k$

设 $f(n, k, 0) = \sum\limits_{i = 0}^{n - 1} [cnt(i) \mod 2 = 0] (i + 1)^k$，$f(n, k, 1) = \sum\limits_{i = 0}^{n - 1} [cnt(i) \mod 2 = 1] (i + 1)^k$

倍增 FFT 即可算出 n 特别特别大的答案！！

要 任 意 模 数 NTT，咕了

### [$CF1349D-slime\ and\ biscuits$](https://www.luogu.com.cn/problem/CF1349D)

绝对有意思的题。这是一个停时问题（我不会鞅和停时定理啊QAQ）看题解去了…… **势能法**比概率做法不知道高到**哪里**去了！

我们希望构造势能函数 $f$，使得每操作一步，$\sum\limits_{i = 1}^n f(a_i)$ 就减少 $1$。（真是神奇的定义啊）

$ans = 初势能 - 末势能 = ( \sum\limits_{i = 1}^{n} f_{a_i} ) - ( f_m + f_0 (n - 1) )$。也就是说 $\sum\limits_{i = 1}^n f_{a_i}$ 要比后继的期望大 $1$。即 $\sum\limits_{i = 1}^n f_{a_i} = \frac{1}{m} \sum\limits_{i = 1}^n a_i ( f_{a_i - 1} + \sum\limits_{j \neq i} (\frac{1}{n - 1} f_{a_j + 1} + \frac{n - 2}{n - 1} f_{a_j}) )$。化得 $\sum\limits_{i = 1}^n f_{a_i} = \sum\limits_{i = 1}^n \frac{a_i}{m} ( f_{a_i - 1} + 1 ) + f(a_i + 1) \frac{m - a_i}{m(n - 1)} + f(a_i) \frac{(n - 2)(m - a_i)}{(n - 1)m}$

再强调一次，是**构造**，怎么方便怎么来。强制钦定每个 $i$ 左右项都相等，则 $f(a) = \frac{a}{m} ( f(a - 1) + 1 ) + f(a + 1) \frac{m - a}{m(n - 1)} + f(a) \frac{(n - 2)(m - a)}{(n - 1)m}$。

这种和相邻若干项有关的方程组叫做 band-matrix。方程组个数为 $n$、与相邻 $d$ 项有关的 band-matrix 消元复杂度是 $O(nd^2)$，大概是竖着消且只消那 $d$/$2d$ 项？[点我](https://www.luogu.com.cn/blog/froggy/qian-tan-gao-si-xiao-yuan-ta-zhan-zhi-band-matrix)

另一种方便的解法是设 $f(i) = k_i f(m) + b_i$。钦定 $f(m - 1) = 0$，解出 $f(0 \cdots m - 2)$，根据 $f(0) = f(1)$ 再推回来。

[$Code$](https://codeforces.com/contest/1349/submission/111783801)

## $\mathcal{4.3}$

### $XJOI1725$

今天题太深了= = 很抱歉我只订了 T1

#### $T1$

好神哦，正解！考虑连辅助边 $(2k + 1, 2k)$, $k \in [0, n / 2]$，那么每组匹配和辅助边共同形成了若干偶环和奇链的并（奇环不可能存在），同时一种偶环和奇链的并对应恰好一组匹配和辅助边（显然），那么 dp 出合法的偶环和奇链的方案，集合幂级数 exp 合并即可。

把 $2k + 1$ 和 $2k$ 视作一个点，一个点看作奇链。$O(2^{n/2} n^2)$。

$c$ 怎么算呢？一个大小为 $x$ 的奇链，贡献 $c^{x - 1}$；一个大小为 $x$ 的环，贡献 $c^x$，因此往每条链的贡献里乘上 $1/c$。

去复习了一遍集合幂级数 exp。我悟了。

---

订正昨天T3 和前天 T3

男人八题

---

AGC014

menci 的序列

ZQC 的迷宫

loj565https://loj.ac/p/565

sdoi2017

SNOI2020

订正前天T3

ULR 打击复读

今日计划：订正 ARC115-F，做完了就去写 https://www.luogu.com.cn/problem/P2612 和 https://www.luogu.com.cn/problem/CF1237E 和 https://www.luogu.com.cn/problem/CF1227G 和 https://www.luogu.com.cn/problem/CF671D 吧！

## 今天的计划应该是——

今天任务：然后去写清训题，再去写 thupc 题，晚上 vp AGC

做 CF 好题呀！

<!-- 今天任务：学拟阵 + 保序回归（） -->

LOJ154!!!
写转置版多点求值
thupc、thuwc
清华集训（选）感觉题质量不是太好
loj6295（线性规划）
uoj448
生成函数：LOJ3102 luogu5577 数树 普通转下降幂
集训队作业：化学竞赛
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