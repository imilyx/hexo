---
title: '300iq contest 1 vp 记录'
date: 2021-04-19 08:12:40
tags: 
mathjax: true
---

老年退役选手~~体验生活~~ 听说 Gym 很好玩？！

写的顺序就是开题顺序 QwQ

## $Free Edges$

就是要切成树，每个连通块答案为 $E - (V - 1)$。

[$Code$](https://codeforces.com/gym/102268/submission/113449450)

## $Best Subsequences$

最小值一定会取，为什么，考虑 shift 到开头。

二分答案。

$dp_i$ 表示以 $i$ 结尾最多能取多少个，权值线段树维护即可。

[$Code$](https://codeforces.com/gym/102268/submission/113449822)

## $Cool Pairs$

两个都动，太蛋疼了，考虑让 $a$ 为 $-n$ ~ $-1$

考虑让一段前缀（按大小顺序）的 $b$ 的贡献都为 $q_i - 1$，即让这些 $b_i$ 都 $= 0$

剩下的补齐。

[$Code$](https://codeforces.com/gym/102268/submission/113450575)

## $Dates$

很「流」。

瞄一眼数据范围，$3e5$！hoho 模拟费用流！

建出费用流模型，发现跑最大费用最大流的话，退流后边权为负，必不会被跑，对应着说明按价值从大往小放没有毛病。

Hall 定理判能否加入。

设 $b_i$ 表示第 $i$ 个女孩子是否选

由于 $L_i \leq L_{i + 1}$, $R_i \leq R_{i + 1}$

我们要满足 $\min\limits_{l, r} ( a_{R_r} - a_{L_l - 1} - \sum\limits_{i = l}^r b_i ) \geq 0$

区间子段最小值可以线段树维护。

[$Code$](https://codeforces.com/gym/102268/submission/113452120)

---

接下来好像难度差不多 ~~（都挺难）~~，顺序开！

upd：

都不会，都要膜题解【绝望】

---

## $Angel Beats$

爷不会带花树，咕咕咕（可能没有下次一定）

## $Expected Value$

数数题！啊~这，300iq 啥都有！我只会高消……

膜一发题解 ——BM！啊学过的全忘了。

$f_i$ 表示 $Pr[t \geq i]$。$ans = \sum f_i$

$g_{i, j}$ 表示走 $i$ 步到 $j$ 的概率，$f_i = \sum\limits_{j = 1}^{n - 1} g_{i, j}$

$g$ 的转移是个矩乘，考虑 BM 求出 $f$ 的递推式 $C$

设 $f$ 生成函数为 $F$，那么有 $F = F * C + F_0$（$F_0$ 是预处理的前 $O(n)$ 项）

答案是 $F(1) = \frac{F_0(1)}{1 - C(1)}$

好妙 qwwwwq！

[$Code$](https://codeforces.com/gym/102268/submission/113480582)

## $Graph Counting$

定理题，打不过；数学题，打不过！

太绝望了…… 权当学习了……

$tutte-berge$ 定理：一个图 $G = (V, E)$ 的最大匹配的大小等于 $\frac{1}{2} \min\limits_{U \subseteq V} ( |U| - odd(G - U) + |V| )$。$odd(G)$ 表示 $G$ 中具有奇数个顶点的连通块个数

必要性显然，充分性不会（[百度百科](https://baike.baidu.com/item/%E5%A1%94%E7%89%B9%E5%AE%9A%E7%90%86/53987625?fr=aladdin) 给出了证明

具体来说，我们考虑所有度数为 $|V| - 1$ 的点组成的集合 $S$，那么剩下的一定是 $|S| + 2$ 个奇团

枚举 $|S|$，剩下的相当于把 $2n - |S|$ 分成 $|S| + 2$ 个奇数；

相当于把 $2n - 2|S| - 2$ 分成 $|S| + 2$ 个偶数；

相当于把 $n - |S| - 1$ 分成 $|S| + 2$ 个非负整数；

相当于把 $n + 1$ 分成 $|S| + 2$ 个正整数；

即，求把 $n + 1$ 分成 $\geq 2$ 个正整数的方案数。

分拆数可以用五边形数求。
那是个啥？【学习笔记】里有。

$O(n \sqrt{n})$。$5s$ 还是能跑滴！

[$Code$](https://codeforces.com/gym/102268/submission/113571306)

## $Hall's Theorem$

构造 2/2…… 想不到啊 QAQ……

你会什么？你只会向前缀连边！

考虑第 $i$ 个点向前 $a_i (a_i \leq i)$ 个点连边，满足 $a_i \leq a_{i + 1}$，这样前 $i$ 个点的 $|N(A)| = a_i$

好的集合个数为 $\sum\limits_{i = 1}^n \sum\limits_{j = 0}^{a_i - 1} \binom{i - 1}{j}$（也就是枚举并强制第 $i$ 个点必选）

随便构造即可！！！

好háo嗨hāi哦ǒu。

[$Code$](https://codeforces.com/gym/102268/submission/113482988)

## $Interesting Graph$

小清新简单题。

不合法的集合，所有点对的路径都在集合内。也就是说该图所有连通块大小 $< 7$。

算出每个连通块用若干种颜色的方案数，然后暴力合并。

点数 $< 7$ 的本质不同连通块很少，所以本质相同的连通块要一起算掉不然会 T。

[$Code$](https://codeforces.com/gym/102268/submission/113569003)

## $Jealous Split$

结论题，打不过

结论：一定存在合法方案，并且方案就是使得每段平方和最小

证明：考虑相邻两段和为 $a$、$b$，$x$ 是 $a$ 最右边的值或 $b$ 最左边的值
  - $a - b \leq x$：显然合法
  - $a - b > x$：令 $a = a - x$，$b = b + x$，趋于合法，且平方和变小

考虑 wqs 二分。但是调物体额外权值的时候可能会跳过正确答案。怎么办呢？

可以合法形成的段个数一定是一个连续区间，我们要尽量让这个区间包含 $K$。

求出以每个位置结尾可以分的段个数区间。

[$Code$](https://codeforces.com/gym/102268/submission/113512492)

## $Knowledge$

做过一遍的题，再碰到还是不会（

暴搜 + 手玩，只有 $12$ 种初始串

给定串不管怎么操作，其对应的初始串都是不变的。

手玩初始串间的转移，矩乘即可。

[$Code$](https://codeforces.com/gym/102268/submission/113564999)