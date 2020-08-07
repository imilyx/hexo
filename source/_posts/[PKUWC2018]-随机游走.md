---
title: '[PKUWC2018]-随机游走'
date: 2020-08-06 15:18:40
tags: 
    - 概率与期望
    - minmax容斥
mathjax: true
---

终于来搞这个早就想搞的题了！！著名（雾）的 $minmax$ 题

据说可以[不用 $minmax$ 容斥](https://www.cnblogs.com/Mychael/p/9229291.html)，设 $f[i, S]$ 表示从 $i$ 出发走完 $S$ 集合的期望 + 把 dp 柿子表示成 $Ax + B$ 的形式就可以做到 $O(n2^n)$ 的复杂度。。。大同小异吧

考虑 $minmax$ 容斥，设 $f[i]$ 为从 $i$ 点出发，到达集合 $S$ 中第一个点的期望时间，那么只要计算 $f[x]$ 就好了。

本题是树的形态，可以推得：

* $x \in S$, $f[x] = 0$
* $x \notin S$, $f[x] = \frac{f[fa_x]}{deg_x} + (\sum\limits_{y \in Son(x)}\frac{f[y]}{deg_x}) + 1$

发现父亲和儿子很不好搞，而每个节点只有一个父亲，我们就用到一个套路：将 $f[x]$ 表示为 $A * f[fa] + B$ 的形式

从下往上做，推得：$A_x = \frac{1}{deg_x - \sum A_y}$, $B_x = \frac{\sum B_y + deg_x}{deg_x - \sum A_y}$

复杂度 $O(n2^n)$。