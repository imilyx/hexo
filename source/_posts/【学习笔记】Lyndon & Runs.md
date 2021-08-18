---
title: '【学习笔记】Lyndon & run'
date: 2021-05-03 16:12:40
tags: 
    - 学习笔记
    - Lyndon分解
    - run
mathjax: true
---

本文中所有字符串比大小都是以字典序为依据。

## **Lyndon**

### lyndon 串

lyndon 串 s **严格**小于 s 所有后缀。设 $\mathcal{L}$ 表示所有 lyndon 串的集合。

若 $a < b \in \mathcal{L}$，则 $ab \in \mathcal{L}$

### lyndon 分解

定义 $CFL(s)$ 是一个关于 $s$ 的划分，其将 $s$ 划分为 $w_1 w_2 \cdots w_k$，所有 $w_i$ 都是 lyndon 串，且 $w_i$ 字典序单调不增或单调不减。

$CFL(s)$ **存在且唯一**。

如何构造 $CFL(s)$？方法还是挺多的。

可以倒做，开个栈维护所有 lyndon 串，出现增时就合并，$O(n)$

还有一种 $O(n)$ Duval 算法：[see oi-wiki!](https://oi-wiki.org/string/lyndon/#duval)

[template(LOJ129)](https://loj.ac/s/1130822)

### lyndon array

$L_i = \max\{j | s[i : j - 1] \in \mathcal{L}\}$

最长的 lyndon 子串不会相交，只会包含：$\forall i < j < L_i$, $L_j \leq L_i$

### 关于后缀

$s[i : L_i - 1] < s[j : L_i - 1](i < j < L_i)$，所以 $suf(i) < suf(j)$

设 $NSV(i)$ 为第一个不满足 $suf(i) < suf(j)$ 的 $j$。$s[i : NSV(i) - 1] \in \mathcal{L}$，因此 $L_i = NSV(i)$。

## **Runs**

### Run

run 是一个三元组 $(l, r, p)$，满足 $p$ 是 $s[l : r]$ 的最小周期，且 $2p \leq r - l + 1$ 且 $s_{l - 1} \neq s_{l - 1 + p}$, $s_{r + 1} \neq s_{r + 1 - p}$（两边不可拓展）

实数 $\frac{r - l + 1}{p}$ 称为该 run 的指数。

本质是对极长严格周期串（$2p \leq |s|$）按照它们的最小周期分类。

### The Runs Theorem

长度为 $n$ 的 run 个数 $< n$，最大的指数和 $\leq 3n - 3$

### Run 的 lyndon root

一个 run $(l, r, p)$ 的 lyndon root 是 $s[l : r]$ 的一个长为 $p$ 的 lyndon 子串。

每个 run 都有 lyndon root。

### 通过 lyndon root 求所有 runs

把串 lyndon 分解，枚举 $i$，一段 lyndon root 必然是 $s[i : L_i - 1]$ 的子串。二分 + 哈希找到包含 $s[i : L_i - 1]$ 的极长循环子串 $s[l : r]$，若满足 runs 定义则找到了一个 run，$s[l : r]$ 可能被统计多次，取周期最小的一次。

对于 lyndon 分解两种情况（单调不增和单调不减）各求一遍 runs，并去重。

[template(LOJ173)，代码超清真](https://loj.ac/s/1130842)

### WPL (Weak Periodicity Lemma)

对于串 $s$，若 $p$ 和 $q$ 都是它的周期且 $p + q \leq |s|$ 则 $gcd(p, q)$ 也是 $s$ 的周期。

### lyndon tree

#### 标准划分

定义 lyndon 串 $s$ $(|s| \geq 2)$ 的标准划分是一个有序对 $(u, v)$ 满足 $v$ 是 $s$ 字典序最小的严格后缀，且 $s = \overline{uv}$。

#### lyndon tree

一棵二叉树，每个节点对应一个 lyndon 串，左右节点是自己的标准划分 $(u, v)$，根节点对应原串 $s$，叶节点对应串长为 $1$。

性质：

1. 若 $s[i : j]$ 是 lyndon 串，那么 $\alpha = lca([i : j]) = [i_{\alpha}, j_{\alpha}]$，满足 $i = i_{\alpha} \leq j \leq j_{\alpha}$
2. 若 $s$ 是 $i$（$i > 1$）开始的最长 lyndon 串，$\alpha$ 一定是个右儿子节点。（否则可以再拼上父亲的右儿子）

### 局限

runs 无法表出非严格周期串。

---

闲话：字符串怎么这么多 theorem 啊 lemma 的啊…… 好难

---

### [集训队作业2018-串串划分](https://uoj.ac/problem/429)

……好吧是因为这题才来学 Runs 的。

两个限制没法一起做。

让每个非法划分里的循环串变成他们最小循环节分割开来的结果，那么不满足 1 变成了不满足 2。

考虑容斥，没有相同看作 $\sum_k (-1^k) * (至少有 k 个相同段的方案数)$

根据这个有 dp：$dp_i$ 表示以 $i$ 结尾的前缀的划分方案数。$dp_i = \sum_j (-1)^{C(s[j + 1 : i]) - 1} dp_j$，其中 $C(s)$ 表示 $s$ 串的最小循环节循环的次数。

$dp_i = \sum_j dp_j - 2 * \sum_j [ C(s[j + 1 : i]\ is\ even) ] dp_j$

引入本原平方串的概念：串 $s$ 是本原平方串，当且仅当其最小正周期为 $s/2$。任何字符串的本原平方串个数是 $O(nlogn)$ 的。

$C(s[j + 1 : i])$ 为偶数，当且仅当 $s[j + 1 : i]$ 由若干相同的本原平方串拼成。而一个本原平方串对应一个 runs。

求出所有 runs。

对于某个 runs $(l, r, p)$，$\forall i \in [l, l + 2p - 1]$, $s[i : i + 2p)$ 是本原平方串。

对于起点 $i$，设置所有 $i + k * 2p$ 为二元组 $(i, p)$ 的关键点。

对于每个二元组维护当前关键点 $f$ 之和。

$O(nlogn)$。

[$Code$](https://uoj.ac/submission/474357)