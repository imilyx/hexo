---
title: 'xza《Graph Theory》趣题选讲'
date: 2020-08-04 20:15:40
tags: 
    - 思维
mathjax: true
---

## [$TC10947-TwoSidedCards$](https://vjudge.net/problem/TopCoder-10947)
-----

正-反，二元关系，想到正反面数字对应连边，每张卡都变成了一条有向边

## [$CF547D-Mike and Fish$](https://codeforces.com/problemset/problem/547/D)
-----

[题解](https://blog.csdn.net/PoPoQQQ/article/details/46125833)

和上一题有异曲同工之妙。行-列，想到二元关系，于是行列建点，每个点都变成了一条有向边。设红色表示入边，蓝色表示出边，我们要给边定向，使得新图每个点的入度出度之差不超过 1。

怎么做？考虑**构造欧拉回路**。复习一波欧拉回路的定义：欧拉回路是经过图 G 中**所有**边的回路。但目前二分图中有偶数个（一定是偶数个，因为保证有解）奇数度点。有奇数度点就很难处理！

可以将奇数度点两两配对（通过连一些无关紧要的辅助边），对于每个连通块选一个初始为奇数度的点跑（注意先跑辅助边），没有就跑初始为偶数度的点，欧拉回路红蓝染色，就很好写。

这个正确性比较显然：欧拉回路上每个点入度出度都想同，每个点连的辅助边最多 1 条，删去辅助边后仍能满足限制。

复习欧拉回路基本写法：套圈法，其实就是把不同的环连在一起，**dfs 函数还是有不少细节的**

``` c++
void dfs(int x) {
    vis[x] = 1;
    for (int i = lnk[x]; i; i = nxt[i]) {
        if (!mark[i]) {
            mark[i] = mark[i ^ 1] = 1;
            lnk[x] = nxt[i];  // 当前弧优化
            dfs(to[i]);
            stk[++top] = id[i];
            i = lnk[x];  // !!!
        }
    }
}
```

## [$TC12330-CoinsGame$](https://vjudge.net/problem/TopCoder-12330)
-----

## [$CF19E-Fairy$](https://codeforces.com/problemset/problem/19/E)
-----