---
title: 浙大ACM第一轮选讲
date: 2020-07-15 08:51:40
tags:
mathjax: true
---

## [7.13 D](https://zjusummer.contest.codeforces.com/group/clkkguw3vK/contest/102643/problem/D)
-----

[挂一个大佬博客](https://www.luogu.com.cn/blog/zyxxs/post-713-d-ti-ti-xie)

为啥第一个柿子等于第二个柿子：

左边式子 = 

P(lca = L) * L +

P(lca = L - 1) * (L - 1) +

P(lca = L - 2) * (L - 2) + 

… + 

P(lca = 0) * 0

重新整理一下就是

P(lca = L) + 

P(lca = L) + P(lca = L - 1) +

P(lca = L) + P(lca = L - 1) + P(lca = L - 2) +

P(lca = L) + P(lca = L - 1) + P(lca = L - 2) + P(lca = L - 3) +

… +

 P(lca = L) + P(lca = L - 1) + P(lca = L - 2) + P(lca = L - 3) + … P(lca = 1)

那么左边就等于 sum{1 to L}{P(lca >= i)}

由于 P(lca >= 0) = 1

那么进而等于 sum{0 to L}{P(lca >= i)} - 1



关于本题卷积：

g(n) = 1 / n! * (-1)^n * (1 - c^{-n * (l + 1)}) / (1 - c^n)

h(n) = 1 / n!

f(n) = \sum_{1 <= j <= n - 1} (n - 1)! * g(j) * h(n - j - 1)

其实就是把组合数拆开来了

最后柿子的 l + 1 是 c = 1 的贡献

不得不说太妙了，杠了一晚上，数学是短板啊

## [7.14 A](https://zjusummer.contest.codeforces.com/group/clkkguw3vK/contest/102647/problem/A)
-----

和[这道](https://codeforces.com/contest/1230/problem/E)一样都用到了 gcd 个数不超过 logn 的性质

## [7.14 B](https://zjusummer.contest.codeforces.com/group/clkkguw3vK/contest/102647/problem/B)
-----

听说是广义 sam + 线段树合并，咕咕（题解已存

## [7.14 C](https://zjusummer.contest.codeforces.com/group/clkkguw3vK/contest/102647/problem/C)
-----

听说是 wqs 二分，咕咕

## [7.14 D](https://zjusummer.contest.codeforces.com/group/clkkguw3vK/contest/102647/problem/D)
-----

有思路的题，对于每个第一步走到的 (x, y)，延长直线经过的整点个数是 n / max(x, y)

也就是说 n / max(x, y) + 1 = m 且 gcd(x, y) = 1

枚举左上半边，x 大于 y 的部分，就是求 n / x(下取整) = m - 1 的 x

**x_min = n / m + 1, x_max = n / (m - 1)**

求个 phi 的前缀和就好了

## [7.14 E]
-----

https://www.cnblogs.com/Flying2018/p/13297095.html

https://www.luogu.com.cn/blog/zkdxlpro/post-713-acm-bu-fen-jian-yao-ti-xie

咕咕咕

## [7.15 A]
-----

算出最大生成树和最小生成树，判断之间是否有斐波那契数存在（必然可以用一些 1 边替换掉 0 边

## [7.15 B]
-----

考虑一个序列自动机（就是每个位置向之后离自己最近的26个字符的位置分别连一条边）

相当于在序列自动机上选一个黑点，选任意个后继染黑

$sg(x) = mex_{T \subset nxt(x)}(XOR_{k\in T}sg(k))$

而 sg 只会是 2^0, 2^1, 2^2...

这也很好想，归纳可得，若当前节点的 sg 是 2^i, 后继的 sg 只会是 2^0, 2^1 ... 2^{i - 1}，选任意个 sg 值异或起来得到的是 0 ~ 2^i - 1，因此 mex 出来就是 2^i。

这就等价于对后继的重标号取 mex，非常喵喵

## [7.15 E]
-----

真就 O(nlogn) 算法满地爬呗。。。比赛时没想有点可惜

可以 dfs 序上建主席树，或者 dsu on tree + 树状数组，或者线段树合并，etc。。

## [7.15 F]
-----

巧妙的构造。题意其实就是构造一个没有等差数列的排列。每次按奇偶位排序，例如奇数位放前面、偶数位放后面，这样就消除了跨越左右的等差数列，分治思想