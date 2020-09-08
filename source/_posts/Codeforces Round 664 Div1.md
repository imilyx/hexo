---
title: 'Codeforces Round 664 Div1'
date: 2020-09-08 13:37:40
tags: 
    - 比赛
mathjax: true
---

China-Round 好神仙 > <

xry 都参与出题啦，我们这届也开始征战四方了！加油，无限可能。

## $A$
-----

小贪心，先分成两个集合，枚举大的集合取几个。

[$Code$](https://codeforces.com/contest/1394/submission/92126211)

## $B$
-----

趣题！要求的是**任何**点都能走回到自己，$k$ 这么小显然指数级枚举啦，问题在于怎么快速判断

考虑 $n$ 条边这个条件，也就是说有 $n$ 个入度，而每个点都能走回到自己，每个点都有一个入度，这意味着 $n$ 条边的终点遍布 $n$ 个点

那么预处理一些东西，只要能实现集合并就好了。$bitset$ 过不去，怎么办？$hash$！$O(1)$ 合并

注意写双$hash$！学习一波新写法 :)

[$Code$](https://codeforces.com/contest/1394/submission/92131053)

## $C$
-----

好题！

最大值最小，考虑二分（判定性问题就可做很多啦！

发现两个串相似的条件是 B 和 N 的个数相等，操作其实是加或减 $B$、$N$、$BN$

那么问题转化为判定是否存在一个 $t$ 使得对于任何 $i$，$dis(s_i, t) \leq mid$

来细化一下：在平面上，设 $s_i$ 对应的点为 $(a, b)$, $t$ 对应的点为 $(c, d)$, $dis(s_i, t) =$
1. $(a - c)(b - d) > 0$: $max{|a - c|, |b - d|}$
2. $(a - c)(b - d) < 0$: $|a - c| + |b - d|$

显然满足异号限制的点必然满足同号限制，即可以看做同号限制对所有 $s_i$ 都适用

化开来 $|x - x_i| \leq mid$, $|y - y_i| \leq mid$，即 $x \in [x_i - mid, x_i + mid]$, $y \in [y_i - mid, y_i + mid]$

对于异号限制，想想怎么把它化作“对所有都适用且刚好限制到异号点”

**这里有个经典trick：$|x| + |y| = |x - y|$，对于同号没有影响，而对于异号是等价的**

所以 $|(x - y) - (x_i - y_i)| \leq mid$，即 $(x - y) \in [x_i - y_i - mid, x_i - y_i + mid]$

至此我们得到了 $x$, $y$, $x - y$ 的限制，枚举一维就能得到另一维的范围，判断有无交就可以了。

[$Code$](https://codeforces.com/contest/1394/submission/92186512)

## $D$
-----

[咕咕，记一下大佬博客](https://blog.aor.sd.cn/archives/1246/)

## $E$
-----

咕咕