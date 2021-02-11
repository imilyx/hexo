---
title: '【学习笔记】LCT'
date: 2020-12-27 17:47:40
tags:
    - 学习笔记
    - LCT
mathjax: true
---

之前学的。以 备 重 修 qwq

## 算法详情

把树剖成许多链，每条链用 splay 维护，中序遍历深度递增。

### access

* 打通 $x$ 到根的通路。过程是先把 $x$ 转到 $x$ 所在 splay 的根，然后让 $x$ 带着 $x$ 的左子树并到 $fa_x$ 所在 splay 中充作 $fa[x]$ 右儿子

* 这个过程 $x$ 和 $fa[x]$ 都和它们原本的右儿子断开了，但右儿子依然保留“父亲是谁”的信息）

### makeroot

* 先 access，此时 $x$ 成了和根在同一 splay 中深度最大、中序遍历最末的点
* 再 splay，此时 $x$ 成了所在 splay 的根，但仍是深度最大。为了让 $x$ 成为根、变得深度最小，我们翻转 $x$ 的左右子树。**注意：此操作不影响其他 splay 的深度顺序。**

### findroot

* 不断跳左儿子
* *珍爱生命，远离 findroot。。。* cut() 里判能否 cut 的部分还是这样吧：
``` c++
if (ch[y][0] == x && !ch[x][1]) {
    cut;
}
```

### *LCT 好像没法同时保证两个节点的子树信息都是对的。。无奈。。*
（模拟赛大爆炸

## 关于 LCT 的时间复杂度分析

[指路周指导博客 ~~对没错我就是贺了一遍~~](https://www.cnblogs.com/zhouzhendong/p/JunTanFenXi.html)

### access

考虑虚实链切换复杂度。

定义重儿子为 $size_x * 2 \geq size_{fa_x}$ 的点，其余为轻儿子。定义势能函数 $\phi$ 为又重又虚的节点数。考虑一次 splay 操作，当前 splay 根节点 $x$ 的情况：

* 若它是轻儿子，则消耗 $1$ 时间，$\Delta \phi \leq 1$
* 若它是重儿子，则消耗 $1$ 时间，$\Delta \phi = 1$

一次 access $x$ 的操作在过程中只会遇到 $logn$ 个轻儿子，因此一次 access 操作的均摊复杂度为 $O(logn)$。

### makeroot、link/cut

考虑过程中的轻重边变化量。

「把根从 $x$ 换成 $y$ 」「切除 $x$ 的子树 $y$」「连接以 $x$ 为根和以 $y$ 为根的连通块」的时候改变的轻重边只会是 $x$ 到 $y$ 路径上的，而前后 $x$ 到 $y$ 路径上都只有 $logn$ 条轻边。$O(logn)$