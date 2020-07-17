---
title: 浙大ACM第二轮选讲
date: 2020-07-17 19:10:40
tags:
mathjax: true
---

## [7.17 A](https://zjusummer.contest.codeforces.com/group/clkkguw3vK/contest/102657/problem/A)
-----

算简单题里的偏难题吧。。

* 操作 2 次数至少为 max{a_i}
* 所有数必须同时减到 0

进行减一后再加倍不会优于先加倍再减一（我也不知道为啥），所以在最大值 max 没进行一次减一操作时让每个数翻倍，尽可能靠近 max；然后一起减一，比 max 小的数在途中必然会有减到 max 的一半的时候，这时候再翻倍就好了。

## [7.17 D](https://zjusummer.contest.codeforces.com/group/clkkguw3vK/contest/102657/problem/D)
-----

[数学大佬的博客](https://www.luogu.com.cn/blog/zyxxs/post-717-d-ti-ti-xie)

反射性质的转化很有意思！代码就先咕咕

## [7.17 E](https://zjusummer.contest.codeforces.com/group/clkkguw3vK/contest/102657/problem/E)
-----

[题解](https://www.kdocs.cn/l/sf6G7oaScGQm?f=201)

很妙的dp！我不会设计状态。。。

## [7.17 F](https://zjusummer.contest.codeforces.com/group/clkkguw3vK/contest/102657/problem/F)
-----

1 <= |B| <= |A|

B 串拼起来的东西只要是 A 的最小循环节就好了。

有个很妙的转化：将与 A 中 [i, j] 匹配的 B 串看作从 i 指向 j + 1 的**有向边**（注意取模）

那么问题就变成了求最小环，floyd 可以跑