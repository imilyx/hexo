---
title: '2018-2019 Winter Petrozavodsk Camp, Oleksandr Kulkov Contest 1'
date: 2021-08-14 14:35:40
tags: 
mathjax: true
---

[☆*:.｡. o(≧▽≦)o .｡.:*☆](https://codeforces.com/gym/102129)

### $E$

$ans = \lceil \frac{ \sum\limits_{i = 1}^n a_i }{2} \rceil$

偶数个石子的堆显然，奇数的考虑每次刨掉总数 $- 1$ 个，先手的优势在于初始那一刨。

### $K$

没什么意思的结论题。在所有的情况中，$a[1]$ 一定为正，$a[2]$ 一定为负，其余每个位置状态任选，$+$ 和 $-$ 恰好抵消。

### $H$

注意两个人都想最大化差值！！而不是最大化「自己 - 对手」！！

$dp_n$ 表示 $n$ 轮的期望

$$
dp_n = \frac{1}{m} \sum\limits_{i = 1}^m max(i - dp_{n - 1}, dp_{n - 1} - i) \\
= \frac{1}{m} \sum\limits_{i = 1}^m |i - dp_{n - 1}|
$$

那这个无穷局是干啥用的呢？

$n \rightarrow \infty$，dp 值可视为不变

$$
x = \frac{1}{m} \sum\limits_{i = 1}^m |i - x| \\
mx = \sum\limits_{i = 1}^m |x - i|
$$

等式右边是个凸函数，且最大斜率就 $m$，所以只会有一个交点

二分交点整数位，根据整数位可以知道斜率（一定是确定的因为这个分段函数分界点都是整数），小数部分就可以解出来。

### $F$

思路显然~~（我都会㗅）~~

从大到小加边，若两个点在所有边的并查集中连通，在树边并查集中不连通，那么他俩均不合法。

具体实现，考虑「所有边的并查集」是在「树边并查集」基础上加边而成的，判点对是否合法只要看点对所在块大小是否相同即可。

每个连通块维护合法点集，启发式合并。

### $J$

Each line contains at most one question mark. ！！！

如果没有 '?'，就是判前 $n - 1$ 行和后 $n - 1$ 行能否匹配，这是 hash 可以解决的事儿。

每种字符出现次数 max 之和只能为 $n$ 或 $n - 1$，枚举补全后 '?' 处全填哪种颜色，hash + map 算方案。

算重是什么情况呢，与填的颜色无关，那就是 '?' 与 '?' 相匹配了，减去就行了。

### $D$

~~好像把常系数齐次递推重新学了一遍 /fad~~

设 $a$ 下标为 $[0, n - 1]$，$F_n = \sum\limits_{i = 1}^k a_{k - i} F_{n - i}$

$$
A = 
\left(
  \begin{array}{l}
  0 & 1 & 0 & \cdots & 0 \\
  0 & 0 & 1 & \cdots & 0 \\
  \vdots & \vdots & \vdots & \ddots & \vdots \\
  0 & 0 & 0 & \cdots & 1 \\
  a_0 & a_1 & a_2 & \cdots & a_{k - 1}
  \end{array}
\right),
F_{n - k} = \left(
  \begin{array}{l}
  F_{n - k} \\
  \cdots \\
  F_{n - 2} \\
  F_{n - 1}
  \end{array}
  \right)
$$

$$
F_n = A F_{n - 1} = A^n F_0 \\
$$

$A^0 = E$。

观察 $A$ 的次幂矩阵每乘一个 $A$，第一行的变化，并用多项式语言表述：

$$
(r_0, r_1, \cdots, r_{k - 1}) \longrightarrow r_{k - 1} \cdot (a_0, a_1, \cdots, a_{k - 1}) + (0, r_0, \cdots, r_{k - 2}) \\
R(x) = \sum\limits_{i = 0}^{k - 1} r_i x^i \longrightarrow x \cdot R(x) \bmod (x^k - \sum\limits_{i = 0}^{k - 1} a_i x^i)
$$

~~不懂就手玩~~

设 $M(x) = x^k - \sum\limits_{i = 0}^{k - 1} a_i x^i$。

$$
A^n = \left(
  \begin{array}{l}
  x^n \bmod M(x) \\
  x^{n + 1} \bmod M(x) \\
  \cdots \\
  x^{n + k - 1} \bmod M(x)
  \end{array}
  \right)
$$

你已经会求 $F_n$ 了。

大功告成一半！接下来要求 $c_i$

$$
A^n F_0 = ( \sum\limits_{i = 1}^k c_i A^{n - b_i} ) F_0
$$

砍掉多余的 $n - b_k$ 个 $A$

$$
A^{b_k} = \sum\limits_{i = 1}^k c_i A^{b_k - b_i}
$$

这就是我们最终要解的等式！

$$
x^{b_k} \equiv \sum\limits_{i = 1}^k c_i x^{b_k - b_i} \pmod{M(x)}
$$

待定系数，左右各是一个 $k$ 个变量（$c_{[1 \cdots k]}$）$k$ 个等式（每个次数一个等式，次数为 $0 \cdots k - 1$）多项式取模求出等式系数后高消。

*坑点：要特判 $K = 1$ 的情况…… 因为 $2 * (K - 1) < K$ 了，fmul 函数（执行多项式模意义下相乘）会出问题。*

### $I$

正难则反，容易想到计算本质不同子串的本质不同子串数和

相当于对于 SAM 上每个节点表示的串 $s[l, r]$，其中 $r = c$, $l \in [a, b]$，求 $[l, r]$ 中本质不同子串数。

用区间本质不同子串直接做！相当于产生 $b - a + 1$ 次询问。可原题我写的偷懒版差分 + 树状数组，我 不 想 写 线 段 树 加 等 差 数 列！！！！

最后还是屈服了…… 其实挺好写的……

加等差数列可以看作加了个斜率为 $-1$ 的一次函数，因为是直线，所以传下去的 tag 根本不用改，非常好写。

tmd调了好几个小时发现 $rotate$ 函数里面写成 $fa[y] = z$ 了！！！我淦！！！

*这道题告诉我不要想着投机取巧*

### $G$

[神仙 yhx 和他的神仙中转站](https://yhx-12243.github.io/OI-transit/records/gym102129G.html)

妙妙妙妙

如果置换存在两个及以上环，结果一定为 $0$

证明：考虑两个环的下标分别为 $c_1, \cdots, c_p$ 和 $d_1, \cdots, d_q$

把 $c_{ 2 \cdots p}$ 列加到第 $c_1$ 列上，$d_{2 \cdots q}$ 列加到第 $d_1$ 列上

第 $c_1$ 列和第 $d_1$ 列整列都相等！所以它们彼此成比例！线性相关了，矩阵不满秩，行列式为 $0$。

然后就不会了 ~~也不想学~~

### $B$

咕了

### $C$

咕了