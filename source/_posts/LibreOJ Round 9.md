---
title: 'LibreOJ Round 9'
date: 2021-04-04 14:49:40
tags:
---

[Jump!](https://loj.ac/p?keyword=LibreOJ%20Round%20%239)

### Tangjz 的背包

$ans = r^p [x^m] \prod\limits_{i = 0}^{n - 1} (1 + \frac{1}{r^i} x)$

设 $q = \frac{1}{r}$，根据[高斯系数-定理3](https://baike.baidu.com/item/%E9%AB%98%E6%96%AF%E7%B3%BB%E6%95%B0/18892160?fr=aladdin)，$ans = r^p * q^{\binom{m}{2}} {n \brack m}_q$（百度百科-定理1 写错了，应为 ${m \brack m}_q = 1$）

${n \brack m}_q$ 是类似组合数的定义。定义 $[n]_q = \sum\limits_{i = 0}^{n - 1} q^i = \frac{q^n - 1}{q - 1}$，$[n]_q! = \prod\limits_{i = 1}^n [i]_q$，${n \brack m}_q = \frac{[n]_q!}{[m]_q![n - m]_q!}$。分子分母都有 $\frac{1}{(q - 1)^n}$，约掉不管。

设 $a$ 为 $q$ 在模 $P$ 意义下的阶，即 $q^a \equiv 1 \pmod{P}$

现在的问题是求解 $F(n) = \prod\limits_{i = 0}^{n - 1} (q^i - 1)$。由于 $q^a = 1$，先统计分子分母模 $P$ 意义下 $0$ 的个数，如果不等显然 $ans = 0$。具体来说，$q^{ai} - 1 = (q^a - 1) ( \sum\limits_{j = 0}^{i - 1} q^{aj} )$, 后面这坨 $= i$, $F(n) = \prod\limits_{i = 0}^{n/a} (q^{ai} - 1) \prod\limits_{i = 1, a \nmid i}^{n - 1} (q^i - 1) = (q^a - 1)^{n/a} (n/a)! \prod\limits_{i = 1, a \nmid i}^{n - 1} (q^i - 1)$。$a$ 是确定的，相当大，不用担心阶乘算不出来啦~

接下来考虑 $\prod\limits_{i = 1, a \nmid i}^{n - 1} (q^i - 1)$。模意义下高斯系数有了更好的性质——${n \brack m}_q = {n \mod a \brack m}_q = {n \brack m \mod a}_q$（随便 yy 一下QAQ），所以这就等于 ${n \mod a \brack m \mod a}_q$ 嘛。

[$Code$](https://loj.ac/s/1108722)

### CommonAnts 的调和数

设所有 $p$ 和 $k$ 的最小公倍数为 $z$，即 $p$ 和 $k$ 都是 $z$ 的因子。

修改会改一些 $z$ 的因子位置和一些不是 $z$ 的因子的位置，询问只问 $z$ 的因子位置。

考虑只改 $z$ 因子位置怎么做，高维前缀和，再做一遍高维后缀和应对查询即可。

不是 $z$ 的因子的位置 $pos$ 的贡献可以考虑放在 $gcd(z, pos)$ 上，即最近的前一个 $z$ 因子位置。所以高维前缀和求完后每个位置 $x$ 还要乘上 $F(n/x) = \sum\limits_{i = 1}^{n/x} i^2 [gcd(i, z) == 1]$，$i^2$ 是因为前缀一遍后缀一遍。$F(n) = \sum\limits_{d | z}^n \mu(d) d^2 \sum\limits_{i = 1}^{n/d} i^2$。

$\sum\limits_{i = 1}^x i^2 = \frac{1}{6} x(x + 1)(2x + 1)$，别再忘了！！~~（丢死人了~~

[$Code$](https://loj.ac/s/1110219)

### Menci 的序列

好巧妙…… 每位如果有值，最多保留两个，剩下的进上去，这样只有可能“进的不够”。然后从第 $K$ 位开始往低走，如果比当前位更高的地方有值，就把它算到当前位（在实际序列中对应删除了一些 '$*$'）；否则就把低位“能进则进”，发现这样并不会进到高于当前位的位置，所以是最优的。

我觉得直接想很难啊。

[$Code$](https://loj.ac/s/1108776)

### ZQC 的迷宫

sb 题…… 一直沿着一边走，显然能在 O(nm) 步内走出来。（话说这不是我逛各种展览时候的策略吗= =）

[$Code$](https://loj.ac/s/1110241)

完结撒花~ (=ﾟωﾟ)ﾉ