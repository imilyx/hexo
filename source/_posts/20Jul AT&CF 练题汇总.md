---
title: '20Jul CF&AT 练题汇总'
date: 2020-07-31 23:59:59
tags: 
    - CF&AT往届
mathjax: true
---

## [CF1039D](https://www.luogu.com.cn/problem/CF1039D)
-----

树的每个节点至多属于其中一条路径!!!

看到题目：这能做？能做？？能做？？？？

对不起，用根号分治就是能做（皮

* k <= sqrt(n) 的暴力算，O(nsqrt(n))
* k > sqrt(n) 的，容易发现答案一定在 (0, sqrt(n)) 的区间内单调不增，最多只有 sqrt(n) 种，所以对于当前的 k 二分出与它答案相同的最右位置，更新这一段的答案

复杂度 O(nsqrt(n)*logn)

HINT：常数巨大，普通 dfs 递归会 T。。要先处理 dfs 序然后从下往上 dp。。。5070ms。。。

据说能用整体二分做，先咕咕

## [CF1039E](https://www.luogu.com.cn/problem/CF1039E)
-----

LCT + 分块，先咕咕

## [CF455D](https://www.luogu.com.cn/problem/CF455D)
-----

操作 1 每次只移动两位，而且操作 2 询问的信息可合并，可以用分块 + 双端队列，O(n * sqrt(n) * logn) cf 神评测机就跑过去了！

## [CF1045B](https://www.luogu.com.cn/problem/CF1045B)
-----

[题解](https://www.cnblogs.com/yyf0309/p/9808323.html)

妙题，容易想到 无法构成的数可以全用 A 中或全用 B 中的数构成。设不能构成的数为 t，A 中构成 t 的数为 X 和 Y。

由模的性质得，若 X < t 则 Y < t, 若 X > t 则 Y > t，所以任何一个 t 将 A 集合划为两半，每一部分首尾配对后，每对的和都等于 t

如何判 1 ~ i 首尾配对的值相同？设 1 <= a < b < c < d <= i, 若 a + d = b + c 则 a - b = c - d，做一个差分然后跑 manacher 或 hash 判回文就好了，妙啊。

## [CF1249D2](https://www.luogu.com.cn/problem/CF1249D2)
------

贪心，我真的不行。。按左端点排序，从左往右扫，大根堆维护右端点，若左端点覆盖次数超过 K 则弹出堆顶线段

## [CF255E](https://www.luogu.com.cn/problem/CF255E)
-----

第一次感觉博弈论没那么高冷哈哈哈

打出 1e6 的表发现 sg 值很小，只为 0 ～ 3。将 a 从小到大排序，尺取法做（就是双指针一样的，左端点挪+右端点挪）。

或者可以前缀和，记录 sum(i, j) 表示 1 ~ i 内 sg = j 的个数，这样就可以 O(1) 算！！

## [CF280C](https://www.luogu.com.cn/problem/CF280C)
-----

一个节点有贡献 1 ，当且仅当它到根的路径上没有点染黑。所以选到它的概率是 1 / dep[x], 期望是 1 * 1 / dep[x]，跟除祖先们外的节点无关（其他不产生影响的就可以不管了，反正对于一个局部来说啥都没发生）

## [CF200A](https://www.luogu.com.cn/problem/CF200A)
-----

暴力优化，非常之厉害

考虑多次询问一个位置，暴力查询的话会重复查询一些距离，开数组存一下就好了。

每次询问时更新，O(能过) 系列但真的跑的很快！

## [CF208B](https://www.luogu.com.cn/problem/CF208B)
-----

n 很小，记忆化搜索。注意到每一位只受后第一位和后第三位影响，状态只要记录连续三位。

## [CF187D](https://www.luogu.com.cn/problem/CF187D)
-----

发现被一个红灯卡了后，之后的花费都是可以预处理的（求一下每个点从 0 时刻走到终点的时间）。把 dist 都 mod(g + r)，问题就变成了求右边最近的 dist 在 [g, r - 1] 内的位置，权值线段树维护区间最小值

## [CF190E](https://www.luogu.com.cn/problem/CF190E)
-----

复杂度是关于 m 的。。链表或 set 优化 bfs，每个点只会被访问度数次，复杂度 O(mlogn)

## [CF191E](https://www.luogu.com.cn/problem/CF191E)
-----

求第 K 大连续区间的 val 和。考虑二分判定，离散化 + 树状数组维护。

## [CF185D](https://www.luogu.com.cn/problem/CF185D)
-----

很好的数学题

设 $x = k^{2^l} + 1$

$k^{2^{l + 1}} + 1 = (x - 1)^2 + 1$

$gcd(x, (x - 1)^2 + 1) = gcd(x, x^2 - 2x + 1) = gcd(x, 2)$

* k 为奇数：ans = $\frac{PROD}{2^{r - l}}$
* k 为偶数：ans = $PROD$

根据等比数列的知识，$PROD = \frac{(k^{2^{r + 1}} - 1)}{(k^{2^l} - 1)}$

## [CF432D](https://www.luogu.com.cn/problem/CF432D)
-----

[讲得好的题解](https://ouuan.github.io/post/cf432d-prefixes-and-suffixeskmp/)

KMP 性质题，f[nxt[i]] = 1 + sum{f[i]}

## [CF1107F](https://www.luogu.com.cn/problem/CF1107F)
-----

贷款有三种：不用，付完，没付完

考虑没付完的贷款，b 大的靠后买更优，所以可以先将 b 从大到小排序

f[i, j] 表示在前 i 个中选，j 个没付完的 C 最大值

## [CF687C](https://www.luogu.com.cn/problem/CF687C)
-----

问能拼出哪些，就想到了 bitset：f[i, j] 表示前 i 个共 j 元能拼出的集合，f[i, j] = f[i - 1, j] | f[i - 1, j - c[i]] | (f[i - 1, j - c[i]] << c[i])

## [LightOJ1422](https://vjudge.net/problem/LightOJ-1422)
-----

区间 dp，f[l, r] = min(f[l + 1, r] + 1, (f[l + 1, i - 1] + 1) + (f[i, r] - 1)(其中 c[l] = c[i]))

小细节是 f[i, r] - 1，由于 i 可能对 [i + 1, r] 同颜色的衣服有影响，所以不是 f[i + 1, r]; 但要减去重复买的钱。

## [CF840C](https://www.luogu.com.cn/problem/CF840C)
-----

[好的题解](https://blog.csdn.net/can919/article/details/80154917)

将相乘为完全平方数的数放在同一组里，问题转化成了经典问题：有多少个排列使得相邻两数不在同一组里。

容斥 + dp，ans = (至少 0 相邻相同的方案数) - (至少 1 相邻相同的方案数) + (至少 2 相邻相同的方案数) ...

dp[i, j] 表示前 i 种数分为 j 块的方案数（无序），相同块中的数相同。即至多 j - 1 不相同，即至少 (n - 1) - (j - 1) = n - j 相同。

$dp[i, k] = \sum{ dp[i - 1, k - j] \times C(ni - 1, j - 1) \times \frac{n_i!}{j!} }$

除以 j! 表示使块无序，乘以 n_i! 表示总排列数（迷惑）

----

upd：[另一好的题解](https://blog.csdn.net/qq_31759205/article/details/77487920)

还是这个好懂且正常👍

转移的时候枚举把当前集合分成多少段，有多少段插在之前相同的相邻元素间

注意由于 size 之和是 n，复杂度不是 O(n^4) 是 O(n^3)

## [CF15E](https://www.luogu.com.cn/problem/CF15E)
-----

[题解题++ qwq](https://www.cnblogs.com/yyf0309/p/8418287.html)

找规律容易发现：路线是从 H 一直沿着边沿斜线走，拐一圈走到 H 正下方第一个点再拐一圈走到另一斜线，走回 H。

但后面就不知道怎么做了qaq？看大佬博客吧。