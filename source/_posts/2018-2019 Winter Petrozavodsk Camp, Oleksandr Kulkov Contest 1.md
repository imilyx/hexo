---
title: '2018-2019 Winter Petrozavodsk Camp, Oleksandr Kulkov Contest 1'
date: 2021-08-14 14:35:40
tags: 
mathjax: true
---

[☆*:.｡. o(≧▽≦)o .｡.:*☆](https://codeforces.com/gym/102129)

### E

$ans = \lceil \frac{ \sum\limits_{i = 1}^n a_i }{2} \rceil$

偶数个石子的堆显然，奇数的考虑每次刨掉总数 $- 1$ 个，先手的优势在于初始那一刨。

### K

没什么意思的结论题。在所有的情况中，$a[1]$ 一定为正，$a[2]$ 一定为负，其余每个位置状态任选，$+$ 和 $-$ 恰好抵消。

### 