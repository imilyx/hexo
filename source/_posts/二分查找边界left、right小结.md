---
title: 二分查找边界left、right小结
date: 2018-08-08 14:50:40
tags: 
    - 计算机常识/初赛
mathjax: true
---

这个也是很没办法，17年的错了，05年的又错了。。。

那么来总结一下常见的几种：

1. left < right, mid = (left + right) / 2, left = mid + 1, right = mid.

2. left < right, mid = (left + right + 1) / 2, left = mid, right = mid - 1.

3. left <= right, mid = (left + right) / 2, left = mid + 1, right = mid - 1.

4. left + 1 < right, mid = (left + right) / 2, left = mid, right = mid(保证每次循环时数组长度都会至少减一，终止时数组长度可能为2(left + 1 == right), 可能为1(left == right), 但绝不会为0(left > right)，因为每一轮循环前总有 left <= mid <= right).

目前碰到的就这些，实数域的不在这儿，但自己写程序时还是专背一种好了。