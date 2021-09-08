---
title: 'EC Final 2019 vp 记录'
date: 2021-05-01 13:14:40
tags: 
mathjax: true
---

[Jump!](https://codeforces.com/gym/102471)

## $A$

签到题，跳

[$Code$](https://codeforces.com/gym/102471/submission/114744542)

## $M$

还是签到题，每类分别枚举，复杂度就是对的。

完球了降智太严重，直接写偏一次，long long 没开又 WA 一次，手写 out() 挂掉了 WA 一次。

[$Code$](https://codeforces.com/gym/102471/submission/114747010)

## $E$

操作：一对边，流量一个减 $1$、一个加 $1$，询问使得流量最大的最小操作数。图的性质是，从 $1$ 到 $n$ 有 $k$ 条等长的除端点外不相交路径。

~~xml 这个憨憨竟然以为每条路径的流量都一样~~

最大流量 $= tot / (m / k)$

对于每条路径，发现移动到这条路径的代价是关于这条路径最终流量的分段函数，并且斜率越来越大。证明显然。

于是只要每次贪心选当前斜率最小的路径即可。

分段函数可以预处理。

一开始把每个分段函数用 vector 维护，再拿指针指着，疯狂 RE + WA，后来把分段函数全都丢进 pq 反倒没有问题。果然我是 STL 黑洞。

[$Code$](https://codeforces.com/gym/102471/submission/114771242)

---

完球了 只会三道

我只会做题解不超过三句话的 sb 题。我是不是完球了！！！！！！！

---

## $H$

瞧这奇怪的回答方式，必有蹊qī跷qiao ~~（woc我一直读xī，然后就一直打不出来拼音~~

一个长度大于等于 $n/2$ 的等比数列，相邻项在实际坐标中，不超过 $2$ 的距离有至少 $n/4$ 对！

对于每种出现次数合法的公比，分别做最长序列。

[$Code$](https://codeforces.com/gym/102471/submission/114778591)

## $C$

还能这么耍？$f^k = g$, 因为 $f^{mod} = \epsilon$（不会证明……）所以 $g^{1/k} = f$，直接狄利克雷卷积快速幂就好了。$O(nlog^2n)$

看了看，还有一种方法是巧妙的倍增递推：[点我](https://blog.csdn.net/weixin_40859716/article/details/108881309)

模数写错了 WA 好几发。wcnm！！！

[$Code$](https://codeforces.com/gym/102471/submission/114812824)

upd on 2021.9.4 在网上看到了 [Freopen 大佬的组合意义证明](https://blog.csdn.net/qq_35950004/article/details/106958131)

大概是：我们要证明 $f^p = \epsilon$，$p$ 是质数、模数

$f^p$ 的组合意义是选 $p$ 个乘积为 $n$ 的数的总方案数，$f$ 的每一项表示选这个数的方案数

$$
f^p(n) = \sum\limits_{\prod a_i = n} \prod\limits_{i = 1}^n f(a_i)
$$

假设已经确定了 $a$ 排序后的到的序列，现在要算给这个序列对应的 $a$ 序列的方案数。假设有 $t$ 种值，每种值有 $b_i$ 个，方案数是个多元二项式 $= \frac{p!}{\prod\limits_{i = 1}^t b_i!} \bmod p$

有个名为库默尔定理的说，$\binom{n + m}{n}$ 含 $p$ 的幂次等于 $n + m$ 在 $p$ 进制下的进位次数。

---

证明：

$$
\binom{n + m}{n} 含 p 的幂次 \\
= \sum\limits_{i \geq 1} \lfloor \frac{n + m}{p^i} \rfloor - \sum\limits_{i \geq 1} \lfloor \frac{n}{p^i} \rfloor - \sum\limits_{i \geq 1} \lfloor \frac{m}{p^i} \rfloor \\
= \sum\limits_{i \geq 1} \lfloor \frac{n + m}{p^i} \rfloor - \lfloor \frac{n}{p^i} \rfloor - \lfloor \frac{m}{p^i} \rfloor
$$

然后你发现每项不是 $1$ 就是 $0$。它实际上等价于 $p$ 进制下 $n$ 与 $m$ 相加**过程中**，第 $i$ 位是否发生进位。

---

总之这个定理就是把组合数和进位联系起来了。

回到上面这个多元二项式，它 $\neq 0$ 当且仅当在 $p$ 进制下将 $b_i$ 累加不发生进位。

但是 $\sum\limits_{i = 1}^t b_i = p$。所以不会在**相加过程中**发生进位的只有 $t = 1, b_1 = p$ 啦。

也就是说 $p$ 个 $a_i$ 完全相同时才对 $f^p(a_1^p)$ 有 $f(a_1)^p$ 的贡献。

$a_1 = 1$ 时对 $f^p(1)$ 有 $1$ 的贡献，$a_2 = 2$ 对 $f^p(2^p)$ 有 $2^p$ 的贡献，但是 $1 < x < 2^p$ 的 $f^p(x)$ 由于没有整数 $p$ 次根，都是 $\bmod p = 0$ 的。因为$f^p$ 仅有 $np$ 项，$n$ 仅有 $10^6$，$np < 2^p$ 所以可以近似认为 $f^p = \epsilon$。也就是说 $n$ 出到 $2^p$ 以上这个结论就错了 /fad

回到原题，我们已经知道了在这题 $n$ 较小的条件下 $f^p = \epsilon, f^k = g,$ 设 $\hat{k}$ 是 $k$ 模 $p$ 意义下的逆元，即 $\hat{k} * k \equiv 1 \pmod{p}$，设 $\hat{k} * k = wp + 1$, 那么 $g^{\hat{k}} = (f^k)^{\hat{k}} = f^{wp + 1} = f$。

## $K$

平面图最大流转最小割转对偶图最短路。

考虑与最外面相邻的边，设其中权值最小的为 $e$，$e$ 靠里那一面会被经过 $0$ 次或 $2$ 次，$2$ 次就一定经过 $e$。于是干脆把 $e$ 的权值分到这个面其他边上，$e$ 消失——用堆维护上述过程，每次除一个环，最终得到一棵树。

再回到平面图最小割的角度看，类 kruskal 重构树的维护一棵最小割树就可以了。

[$Code$](https://codeforces.com/gym/102471/submission/114813921)

## $J$

显然最小值的位置永远不动。但这个性质太弱了。

性质：

对于某个最小值所在的 $i$，若 $minpos_{[1, i - 1]} \in [i - c, i - 1]$，$[i - 2c, i - 1]$ 在满足 $minpos_{[i - 2c, i - 1]} \in [i - c, i - 1]$ 的情况下可以随意排列。（把 $minpos$ 放到 $i - k(k \in [1, c]$，随意换，然后再以 $i$ 为 $minpos$ 随意换，这样就能把 $[i - 2c, i - c - 1]$ 的元素搞过来了）

同理若 $sempos_{[1, i - 1]} \in [i - c, i - 1]$，$[i - 3c, i - 1]$ 在满足 $sempos_{[i - 3c, i - 1]} \in [i - 2c, i - 1]$ 的情况下可以随意排列。

以此类推。

于是我们得到了一个递归解法：$solve(L, R, vl, vr)$，表示从小到大考虑，靠左侧有 $vl$ 个，靠右侧有 $vr$ 个

- 若 $(vl + vr) * c > R - L + 1$ 那么剩下的数可以在 $R - L + 1$ 中随便放，大概有点「管辖」的意味。
- 否则考虑考虑当前区间未放的最小数字在 $k$，$k$ 最多只能被一侧管辖（否则就是前面那种情况啊）
  - 如果能被管辖，递归到管辖它的那边继续做
  - 否则返回 $solve(L, k - 1, vl, 1) * solve(k + 1, R, 1, vr)$

[$Code$](https://codeforces.com/gym/102471/submission/114816185)

## $D$

要求遍历一棵树的出发时间最晚。每个点要恰好施魔法一次。$a \rightarrow b$ 当且仅当 $t_a > 0$ 且 $t_b \geq 0$

由于最终的路径一定是走完整个子树，或者还差一段。设 $f_x$、$g_x$ 分别表示前者和后者的最晚进入时间。

假设我们已经求出了每个儿子的信息。

假设遍历 $x$ 所有儿子子树的顺序是 $y_1, \cdots, y_k$，我们要一种最优的 $y_i$ 排列使得 $f_x$ 最大。

$f_x = \min\limits_{i = 1}^k ( f_{y_i} - \sum\limits_{j < i} 2size_{y_j} )$

微扰一下，猜测按照 $f_{y_i} + 2size_{y_i}$ 升序排列。感性理解这是对的。

计算 $g$ 需要枚举走到哪个子树里停住了。前后缀维护一下，计算与 $f$ 类似。

[$Code$](https://codeforces.com/gym/102471/submission/114824781)

## $L$

问选两条路径，不重复覆盖一个点超过 $k$ 次的方案数。$k \leq 1e9$。

不会，咕了

## $B$

假设当前 $a - b = x$

一次走一步没法判断 $x$ 的变化，考虑一次走两步

1. 👆👆
2. 👉👉
3. 👆👉
4. 👉👆

$1$ 和 $2$ 对 $x$ 和横纵坐标的奇偶性无影响，$3$ 和 $4$ 则对这俩都有影响

枚举 $3 + 4$ 的个数 $p$，$4$ 的个数 $q$

有关系柿: $\lfloor \frac{p + 1}{2} \rfloor - q = k$，因为只有在纵坐标为偶数的时候走 $3$ 才 $+1$，

那么枚举 $p$，贡献是 $\binom{(n + m)/2}{p} * \binom{p}{q} * \binom{(n + m)/2 - p}{(n - p)/2}$

[$Code$](https://codeforces.com/gym/102471/submission/114830195)

剩下几道奇怪的模拟、计算几何、随机化，咕了咕了