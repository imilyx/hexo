---
title: 'Atcoder Regular Contest 102'
date: 2018-09-09 15:02:40
tags: 
    - 比赛
mathjax: true
---

[比赛链接](https://arc102.contest.atcoder.jp/assignments)

### C. Triangular Relationship
-----

因为只是两个数相加，所以可以分成两种情况：

1. a 和 b 都是 k 的倍数。即，!(a % k) && !(b % k).
2. a 和 b 之和是 k 的倍数。即，(a + b) % k = 0, 