---
title: '【学习笔记】Splay代码调整笔记'
date: 2020-02-04 11:50:40
tags: 
    - 学习笔记
mathjax: true
hidden: true
---

这篇博客的目的：力求固定一个优美而优秀的 splay 模板。

* 在区间的左右各加一个“哨兵”，不容易 RE

* 下放标记和上传标记分开写

* 下放标记记录子节点的信息，所以对 x 打标记时要同时处理好这个节点。 简而言之，均采用“记录儿子信息”的方式。 线段树也这么干。

* 关于双旋：
``` c++
void rotate(int x) {
    psd(fa[x]), psd(x);
    int y = fa[x], z = fa[y], k = (ch[y][1] == x);
    ch[z][ch[z][1] == y] = x, fa[x] = z;
    ch[y][k] = ch[x][k ^ 1], fa[ch[x][k ^ 1]] = y;
    ch[x][k ^ 1] = y, fa[y] = x;
    upd(y), upd(x);
}

void splay(int x, int goal) {
    for (; fa[x] != goal; rotate(x)) {
        int y = fa[x], z = fa[y];
        if (z != goal) rotate((ch[y][0] == x) ^ (ch[z][0] == y) ? y : x);
    }
    if (!goal) root = x;
}
// 以后就这么干！！！
```

* 关于查询

要记得下传 root 和 ch[root][1]（区间的前驱和后继）的标记，这是一种习惯(^з^)-☆

* 哪里 psd？

总结了下，rotate、findKth、还有各种查询 [l, r] 信息的函数（详见上面👆那条：关于查询）。

* 哪里 upd？

总结了下，init、build、rotate，还有insert、delete、reverse之类将 l - 1 和 r + 1 伸展到根和根右节点的函数，操作完要 upd。