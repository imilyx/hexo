---
title: 'Week2测试题解'
date: 2020-07-15 08:14:40
tags: 
    - 比赛
mathjax: true
---

## [AIZU2989](https://vjudge.net/contest/381744#problem/B)
-----

giao，全想着 L、R 逆推了，但是写挂了，爬。。实际上有更好写的做法

发现 ai 相对顺序无关，可以先从小到大排序。容易发现操作都是 小于某数的向左 大于某数的向右，ai 一直具有单调性（这个我想到了。。）  二分就好了

## [AIZU2990](https://vjudge.net/contest/381744#problem/C)
-----

注意，初始只能往右走。就是个 sb 题！！题目读错了哭

一直在想 set，但这题用 set 会很麻烦。

序列差分，算出每个点经过的次数 x，来回的次数就是 max{x}，减去的前后缀也必定是 经过 x 次点的最左和最右。。。

## [AGC041B](https://atcoder.jp/contests/agc041/tasks/agc041_b)
-----

不错的题，容易想到答案满足单调性，可以二分。二分的位置只要奋力超过或赶上 p 即可，那么 m 位评委尽量不投 p 就好了。

## [AIZU2992](https://vjudge.net/contest/381744#problem/E)

-----

不是很难的dp，可惜题目读不懂。。

显然 a，r，b 从大到小排序最优。dp，红色总数不能超过 totr，黑色总数不能超过 totb，所以 f[i, j, k] 表示前 i + j 中有 i 红，j 黑，红色总数为 k 的最大不改变颜色的气球数量

## [AIZU2995](https://vjudge.net/contest/381744#problem/F)
-----

好题！

考虑整棵树，每个点选 ci 还是 di，这跟“树”这个结构没有关系，就是一个经典问题：一个 n 个点 m 条边的图，每条边连接 ci 和 di，可以染黑 ci 或 di，问最大黑点数

显然是 min{|V|, |E|}：首先最多有 |V| 个，其次如果边为 V - 1（是树）那就是 V - 1

min{|V|, |E|} = |V| - 1 + [是否存在非树边]，并查集维护就好了。

对于每个子树：dsu on tree。考虑到 有删除操作，用可撤销并查集维护。是真的难写（

## [AIZU2991](https://vjudge.net/contest/381744#problem/G)
-----

2^{n+1} 中选 2^n 个，也就是选一半

v 和 v ^ X 必然选一个，&值为 A 的对必然不选一个，|值为 O 的对必然不选一个（找这样的对，枚举子集即可

2-sat，输出卡行末空格恶心了我半天

## [AIZU2994](https://vjudge.net/contest/381744#problem/H)
-----

C = V - E + F

C：连通块数   V：点数   E：边数   F：内部区域数

（注意网格图经常考这个）

由期望的线性性得，E(C) = E(V) - E(E) + E(F)

而本题是树，F 为 0

所以 E(c1c2) = E(v1v2) + E(e1e2) - E(v1e2) - E(v2e1)

* 点点贡献：可以推柿子（别忘了组合数），也有更灵活的解法。考虑一对点 (a, b) 的贡献（a 在树 1 中，b 在树 2 中），是 1 / 4, 而总共有 n(n - 1) 对点，所以就是 n(n - 1) / 4

* 点边贡献：枚举树 2 中一条边 (u, v)，与树 1 中点 x 的贡献是 1 / 8, 总共是 (n - 1)(n - 2) / 8

* 边边贡献：这个就不能 O(1) 算了，枚举树 2 中一条边 (u, v)，与树 1 中边的总贡献是 (n - 1 - u和v在树 1 中连的边) / 16

套路，据我哥说在他们那个时候，乘积期望也是套路题哇！多练多练