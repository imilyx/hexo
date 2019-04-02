---
title: '[CF1C]-Ancient Berland Circus'
date: 2019-03-03 23:30:40
tags: 
    - 计算几何
mathjax: true
---

[题目链接](http://codeforces.com/problemset/problem/1/C)

平生第一道计算几何 ( *｀ω´) 有点被吓到了，创世纪初的 CF T3 都是这种风格的嘛 ∑(ﾟДﾟ)

其实题目还挺友好。这个多边形中心到给定的三个点距离相等，设这个距离为 dis、多边形中心为 O，我们以 O 为圆心，以 dis 为半径作圆，这就是俗称的“外接圆”啦。三个点也都在这个外接圆上。

我们发现，多边形边数越少，其面积越小。