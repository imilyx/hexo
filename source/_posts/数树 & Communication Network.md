---
title: '数树 & Communication Network'
date: 2021-04-27 16:08:11
tags:
    - 生成函数
mathjax: true
---

多久没有单独开篇了 = = 碰到两道 trick 类似的，来写写。

结论：$f(S) = \sum\limits_{T \subseteq S} \sum\limits_{P \subseteq T} (-1)^{|T| - |P|} f(P)$。

证明：规定 $0^0 = 1$，只有当 $P = S$ 当时候会被计算进 $f(S)$。

似乎有道严格弱化版 [$CF917D$](https://www.luogu.com.cn/problem/CF917D)。真的很弱。

## [$Communication Network$](https://loj.ac/p/3399)

感觉是个 prufer 大杂烩啊…… 设 $f(v)$ 表示 $|E1 \cap E2| = v$ 的方案数，连通块嘛，树形 dp？然后乘个 prufer？容斥？复杂度爆炸！

试试我们的今日 trick。

$$ans = \sum\limits_{T_2} \sum\limits_{S \subseteq ( T_1 \cap T_2 )} \sum\limits_{T \subseteq S} (-1)^{|S| - |T|} |T| 2^{|T|}$$

$g(S)$ 表示**包含** 边集 $S$ 的 $T_2$ 个数，$g(S) = n^{k - 2} \prod\limits_{i = 1}^k a_i$，其中 $k = n - |S|$

通过各种靠近二项式定理，可以推得：

$$ans = 2 \sum\limits_{S \subseteq T_1} |S| g(S) = \frac{2}{n^2} \sum\limits_{S \subseteq T_1} |S| \prod\limits_{i = 1}^{n - |S|} n a_i$$

$a_i$ 的组合意义是 $\binom{a_i}{1}$。于是采用 $CF917D$ 的 dp 方式：$dp_{x, 0/1, 0/1}$ 表示 $x$ 子树，$x$ 所在连通块是否选了点、是否选了边，的贡献和。树形背包合并。

[$Code$](https://loj.ac/s/1125813)

## [$WC2019-$数树](https://loj.ac/p/2983)

算是“网红”题目了？打卡！数树、数数、数鼠、鼠树…… /yun

### $op = 0$

设重边数为 $cnt$，$ans = col^{n - cnt}$

### $op = 1$

这个 subtask 和上一道简直一毛一样

推出来 $ans = \sum\limits_{S \subseteq T_1} col^{n - |S|} (1 - col)^{|S|} g(S)$，$g(S)$ 表示包含边集 $S$ 的树个数

$$ans = \frac{(1 - col)^n}{n^2} \sum\limits_{S \subseteq T_1} \prod\limits_{i = 1}^k \frac{n col}{1 - col} a_i$$

考虑 $a_i$ 组合意义，$\binom{a_i}{1}$，于是可以 dp, $dp_{x, 0/1}$ 表示 $x$ 所在连通块有无选点

### $op = 2$

换汤不换药。

因为 $T_1$ 也不确定了，每个块方案数还要算上树的形态方案数，即 $n^{n - 2}$，同时编号也有待分配，因此对 $g(S)$ 要魔改一发，最后：

$$ans = \frac{(1 - col)^n}{n^4} \sum\limits_S \prod\limits_{i = 1}^k \frac{n^2col}{1 - col} a_i^2 a_i^{a_i - 2} * \binom{n}{a_1, \cdots, a_k}（分配编号）$$

ho~ 这下没法 dp 咯，转战生成函数。

考虑 EGF 和 exp 的组合意义，拼一下就好了。

具体来说，设 EGF $F(x) = \sum\limits_{i \geq 1} \frac{n^2 col}{(1 - col) i!} i^i x^i$, $G(x) = e^{F(x)}$，$ans = \frac{(1 - col)^n n!}{n^4} [x^n] G(x)$

~~有个靠谱的全家桶真好 qwq~~

[$Code$](https://loj.ac/s/1126517)