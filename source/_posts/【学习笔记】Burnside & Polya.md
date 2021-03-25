---
title: '【学习笔记】Burnside & Polya'
date: 2021-03-19 10:53:37
tags:
    - 群论
    - 学习笔记
mathjax: true
---

[oi-wiki](https://oi-wiki.org/math/permutation-group/)

### $Burnside$ 定理

$|X/G| = \frac{1}{|G|} \sum\limits_{g \in G} |X^g|$

其中
* $X$ 是不考虑本质不同的染色方案集合
* $G$ 是各种翻转啊旋转啊等操作构成的置换群
* $X/G$ 是本质不同的染色方案集合
* $X^g$ 是在置换 $g$ 意义下 $X$ 中不动点个数（不动点表示 $g(x) = x$）

### $Polya$ 定理（$Burnside$ 的显然推论）

大概其诞生之意义是解决环上问题？~~也可能我 $\mathcal{Naive}$ 了~~

$|X/G| = \frac{1}{|G|} \sum\limits_{g \in G} |M|^{c(g)}$

其中
* $M$ 是颜色数
* $c(g)$ 是置换 $g$ 的不相交循环置换个数

### [$Polya 板题$](https://www.luogu.com.cn/problem/P4980)

这题我们直接套公式就可以做！
$ans = \frac{1}{n} \sum\limits_{g \in G} n^{c(g)} = \frac{1}{n} \sum\limits_{i | n} \varphi(n / i) * n^i$

设 $g$ 旋转了 $i$，则 $c(g) = gcd(n, i)$，因为某个点在置换 $g$ 的重复作用下经过 $lcm(n, i)$ 次操作回到自身，所以 $c(g) = \frac{n * i}{lcm(n, i)} = gcd(n, i)$

枚举 $c(g)$，系数为 $\varphi(n / i)$，因为 
$\sum\limits_{k = 1}^{n} [gcd(n, k) == i]= \sum\limits_{k = 1}^{n / i} [gcd(n / i, k) == 1] = \varphi(n / i)$

$O(\sqrt{n})$。

### [$Magic\ Bracelet$](https://vjudge.net/problem/POJ-2888)

有限制，填色方案那里要魔改一发：$ans = \frac{1}{n} \sum_k f(k) \varphi(n / k)$，$f(k)$ 表示 $c(g) = k$ 的方案数，显然等于长度为 $k + 1$ 且首尾颜色相同的链的方案数，矩阵加速即可。

类似的还有「如果奇迹有颜色」，写在「常系数齐次线性递推」里。

### [$洛谷4128-有色图$](https://www.luogu.com.cn/problem/P4128)

染色的是边，转的是点。某置换的不动点个数等于 $m$ 的「边等价类个数」次方。要求边等价类个数，就要分_连接同个循环的点_和_连接不同循环的点_讨论。

同个循环：设长度为 $b_i$，边等价类个数为 $b_i / 2$

不同循环：边等价类大小为 $lcm(b_i, b_j)$，边等价类个数为 $b_i * b_j / lcm(b_i, b_j) = gcd(b_i, b_j)$

现在知道了具体 $b_i$ 就能算答案了。
考虑 $n$ 的分拆，$7e7$ 左右，十分稳

我们枚举 $b$ 的多重集，系数也挺正常的：$\frac{n!}{\prod b_i!} * \prod (b_i - 1)! * \frac{1}{c_i!}$

其中第一项是把点分配到多重集里，第二项是每个集合排顺序（是置换嘛），第三项是除掉大小相同的集合算重的方案。

有双倍经验题：HNOI2009-图的同构计数。第一反应是无标号连通图计数 + 背包——不对劲，我只会有标号的。再一想，边的有无不正对应着此题中染成黑或白色吗？于是看题解，果真就是有色图的卡常版——把 gcd 在中途计算掉就能 AC 了。

三倍经验题：[（校内模拟赛）种族](https://dev.xjoi.net/contest/1633/problem/2)，还要讨论一条边跨越两个循环和跨越三个循环的情况。