---
title: 'XXI Opencup GP of Tokyo'
date: 2021-07-31 09:48:40
tags: 
mathjax: true
---

[Jump!](https://codeforces.com/gym/102978)

### $B$

傻了，& 和 | 操作没有可下手之处，但是操作等价于选一个位置，删除它的一个 neighbor

枚举最后赢家是哪个 $1$。

$dp_i$ 表示 $i$ 个用这种删除方法删光的方案，$dp_i = dp_{i - 1} * (2 * i - 1)$，表示 $i$ 种「$i$ 最后删除」的方案，$i - 1$ 种「$i$ 作为邻居被删除」的方案

### $I$

必然是一段上升子序列选最长的，剩下就是凑数用。
填数可以填在小于自己的 $x_i$ 前面，方案乘积为答案。

### $H$

考虑期望的线性性，考虑每个 $b_i$ 的贡献。
它产生贡献当且仅当它在所有 $a_i$ 删完之前删了

考虑容斥，枚举**至少**在它之后删的 $a$ 中集合 $S$，容斥系数 $(-1)^{|S|}$

理智推柿：

$\sum\limits_{S \subseteq U} (-1)^{|S|} \sum_i (1 - \frac{\sum\limits_{j \in S} a_j + b}{sum})^i \frac{b}{sum}$

$= \sum\limits_{S \subseteq U} (-1)^{|S|} \frac{b}{\sum\limits_{j \in S} a_j + b}$

只和 $\sum a_j$ 有关，dp 即可。

为什么不能直接算 $b$ 在所有 $a$ 之后的概率？题设中的概率计算方式，限制我们只能从前往后，不能倒着妄图 $\frac{b}{b + \sum\limits_{j = 1}^n a_j}$。

### $F$

woc 题目读错了，只要 $n - 1$ 和 $n$ 的 lca

设 $lca(n - 1, n) = x$，$sz[x] = S$ 的方案数是 $\frac{(S - 1)!}{2}$。证明大概考虑补集转化，总数为 $(n - 1)!$，然后任意一个 $lca = x$ 某个儿子的方案都可以将 $n - 1$ 所在分支子树拆出来挂成 x 儿子，即合法方案和不合法方案之间构成双射。

这个方案数要乘给关于 $S$ 的 OGF 里。分治 FFT 卷这个 OGF，$x$ 子树的贡献 $(x - 1) * a_x * \prod_i (1 + a_iz)$，第一项表示 $x$ 选父亲。

### $E$

普通图的匹配方案数是 np 问题吧…… 事有蹊跷

注意到每个点出入度都至多为 2，4-sat？AB 用处不止于此。完全不会。

官解：

$B \leq \sqrt{n}$ 时状压即可。维护一个状态条，一格格移。

否则：$B$ 大，可是 $n / B$ 小啊！

设 $g = gcd(A, B)$

若 $g = 1$，将每个点对应到一个 $(n / B) * B$ 的矩阵，点 $v$ 对应到 $(x, y)$ 满足 $x = \lfloor \frac{v}{B} \rfloor$, $yA \equiv v \pmod{B}$，在方格图上从左往右做状压。

关于细节：需要高维前缀和，需要枚举第一列内部配对状态以及第一列指向第二列的配对状态。

若 $g > 1$，则为了让点与格点的映射关系不串，需要分开做然后方案数乘起来。（同余什么的好玄学啊…… 自己画几组小样例玩玩）

### $C$

上次咕了的题，这次一定！（订完这道就去写数学作业

$\sum\limits_{b = 0}^B \sum\limits_{r = 0}^R \binom{b + r}{b} \binom{(B - b) + (R - r)}{B - b} \min( \frac{r}{b}, \frac{R - r}{B - b} )$

$= \sum\limits_{A = 1} \sum\limits_{b = 0}^B \sum\limits_{r = 0}^R \binom{b + r}{b} \binom{(B - b) + (R - r)}{B - b} [bA \leq r] [(B - b)A \leq (R - r)]$

$= \sum\limits_{A = 1} \sum\limits_{i = r - bA = 0}^{R - B} \sum\limits_{b = 0}^B \binom{b + r}{b} \binom{(B - b) + (R - r)}{B - b}$

即将 $b$ 视为 $x$，$r$ 视为 $y$，$y = Ax +$ 一个常数 $i$，构成了一个平行四边形形状的区域，枚举路径经过的点 $(b, Ab + i)$, 说明要统计的是所有路径经过这个区域的点的次数和。

先来解决前置问题：
求解 $f(W, A, P)$ 表示从 $(0, 0)$ 到 $(W, AW + P)$ 且中途不跨过 $y = Ax + P$ 的路径条数。

枚举第一次跨过的位置：

$f(W, A, P) + \sum\limits_{i = 0}^{W - 1} f(i, A, P) \binom{ (A + 1)(W - i) - 1 }{ W - i } = \binom{(A + 1)W + P}{W} (I)$

考虑一条从 $(0, 0)$ 到 $(W - 1, AW + P + 1)$ 的路径，其必然跨过 $y = Ax + P$（题外话：你一直记不住的 Catalan 数就是这么推的，$(0, 0)$ 到 $(n, n)$ 的路径 $- (0, 0)$ 到 $(n - 1, n + 1)$ 的路径 $= \binom{2n}{n} - \binom{2n}{n - 1}$ ）

$\binom{(A + 1)W + P}{W - 1} = \sum\limits_{i = 0}^{W - 1} f(i, A, P) \binom{ (A + 1)(W - i) - 1 }{ W - i - 1 } = \sum\limits_{i = 0}^{W - 1} f(i, A, P) \frac{1}{A} \binom{(A + 1)(W - i) - 1}{W - i} (II)$

$(I) - A(II)$ 得出 $f(W, A, P) = \binom{(A + 1)W + P}{W} - A \binom{(A + 1)W + P}{W - 1}$

考虑计算某条路径 $y = Ax + P$ 被所有 $(0, 0)$ 到 $(W, H)$ 直线经过的次数和，记为 $g(W, H, A, P)$，保证 $H \geq AW + P$

枚举路径上的点：$g(W, H, A, B) = \sum\limits_{i = 0}^W \binom{(A + 1)i + P}{i} \binom{ W + H - (A + 1)i - P }{ W - i }$

这么快速得到 $g$ 呢？我们还和之前 $f$ 一样，考虑 $g(W, H, A, B) - A g(W - 1, H + 1, A, B)$

$= \sum\limits_{i = 0}^W \binom{ (A + 1)i + P }{ i } ( \binom{W + H - (A + 1)i - P}{W - i} - \binom{W + H - (A + 1)i - P}{W - 1 - i} )$

$= \sum\limits_{i = 0}^W \binom{ (A + 1)i + P }{ i } f( W - i, A, H - AW - P )$

其组合意义也很好想，$(0, 0)$ 走到 $(i, Ai + P)$，再在不跨过 $y = Ax + P$ 的情况下走到 $(W, H)$（未来的你会绕晕吗，如果会，那你比现在的我逊 /大杯 但是这个组合意义显然正确）

实际上这玩意 $= \binom{W + H + 1}{W}$！！！没想到吧，和 $A$、$P$ 无关（xyx：$A$、$P$ 只是我们对路径的分类方式）

可以看作，枚举 **最后一次碰到** $y = Ax + P$ 的位置 $p$，在 $p$ 位置向上走一格，走到 $(W, H + 1)$ 的方案数。

因此我们终于得到了：
$g(W, H, A, B) - A g(W - 1, H + 1, A, P) = \binom{W + H + 1}{W}$

$g(W, H, A, B) = \sum\limits_{i = 0}^W \binom{W + H + 1}{i} A^{W - i}$

结 果 与 $P$ 无 关

最后一步了。因为结果和 $P$ 无关，所以 $A$ 相同的线段合在一起算。

$ans = \sum\limits_{A = 1}^{R/B} (R - AB + 1) \sum\limits_{i = 0}^B \binom{R + B + 1}{i} A^{B - i}$

$= \sum\limits_{i = 0}^B \binom{R + B + 1}{i} [ (R + 1) \sum\limits_{A = 1}^{R / B} A^{B - i} - B \sum\limits_{A = 1}^{R / B} A^{B - i + 1} ]$

法一：

伯努利数计算自然数幂和即可。
什么你不知道伯努利数？[owo](https://www.cnblogs.com/Judge/p/10722777.html)

伯努利数满足 $B_0 = 1$, $\sum\limits_{i = 0}^{n - 1} \binom{n}{i} B_i = 0$

$nlogn$ 求解伯努利数：
$\sum\limits_{i = 0}^{n - 1} \binom{n}{i} B_i + B_n = B_n$

$\sum\limits_{i = 0}^{n} \binom{n}{i} B_n = B_n$

$\sum\limits_{i = 0}^n \frac{B_i}{(n - i)!i!} = \frac{B_n}{n!}$

EGF 出来了。准确来说，$B(x) = \sum\limits_{i \geq 0} (\frac{B_i}{i!} + [i == 1]) x^i$

即 $B(x) e^x = B(x) + x$

$B(x) = \frac{x}{e^x - 1}$

法二：

计算 $e^x + e^{2x} + \cdots + e^{(R/B)x}$，等比数列求和，$\frac{ 1 - e^{((R/B) + 1)x} }{ 1 - e^x }$

upd: 我的全家桶太慢了，卡了别人板子才过的…… 烦

woc 好好一场 XXI，一道 C 题占一半篇幅 /tuu

### $A$

写在「LGV」

### $J$

咕咕咕