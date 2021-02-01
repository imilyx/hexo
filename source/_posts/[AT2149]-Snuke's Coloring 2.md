---
title: "[AT2149]-Snuke's Coloring 2"
date: 2020-01-13 20:15:40
tags: 
    - 线段树
    - 单调栈
mathjax: true 
---

ARC 的 D 啊这是！！！毒瘤 qwq。。不过挺有意思的

[传送门！飞](https://vjudge.net/problem/AtCoder-2149)

[这位dalao说得好，我若讲不清楚可看ta的！](https://www.cnblogs.com/coco-night/p/9677691.html)

首先，一切的一切之前！要先公布一个性质。。（什么时候才能自己找啊qwq），就是这个矩形周长一定大于等于 $2 * max(w, h) + 2$, 那么这个矩形**一定**过 $x = \frac{w}{2}$ 或 $y = \frac{h}{2}$ 中的一条，接下来以后者为例。（别忘记做完后将图翻转再做一次前者）

有了**过中线**的保证，我们就可以把点分成中线上下两部分处理。

找！定！量！这个定量就是一条竖着的扫描线，身份是矩形的右边界。。$O(n ^ 2)$的朴素算法就是从右边界往左扫，单调栈维护**最**值。考虑如何优化。

用线段树**帮助**维护扫描线左边（即扫过区域）每个点所在直线为左边界时的最大矩形周长，加加减减具体可看 dalao 博客链接，其核心就是线段的和差。。显然是可以维护的！！

code :
``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (long long i = x; i <= y; i++)
#define x first
#define y second
using namespace std;

const int N = 3e5 + 10;
typedef pair<int, int> pii;
int w, h, n, ans;
int tag[N << 2], mx[N << 2];
pii p[N], a[N], b[N];

void psd(int x) {
    if (tag[x]) {
        tag[x << 1] += tag[x];
        tag[x << 1 | 1] += tag[x];
        mx[x << 1] += tag[x];
        mx[x << 1 | 1] += tag[x];
        tag[x] = 0;
    }
}

void update(int x, int l, int r, int lx, int rx, int val) {
    if (rx < l || r < lx) return;
    if (lx <= l && r <= rx) {
        tag[x] += val, mx[x] += val; return;
    }
    psd(x);
    int mid = (l + r) >> 1;
    update(x << 1, l, mid, lx, rx, val);
    update(x << 1 | 1, mid + 1, r, lx, rx, val);
    mx[x] = max(mx[x << 1], mx[x << 1 | 1]);
}

void solve() {
    memset(mx, 0, sizeof(mx));
    memset(tag, 0, sizeof(tag));

    sort(p + 1, p + n + 1);

    int l = 0, r = 0;
    rep(i, 1, n - 1) {
        if (p[i].y <= h / 2) {
            int nxt = i - 1;
            while (l && p[i].y > a[l].y) {
                update(1, 1, n, a[l].x, nxt, a[l].y - p[i].y);
                nxt = a[l].x - 1;
                --l;
            }
            if (nxt != i - 1) a[++l] = make_pair(nxt + 1, p[i].y);
        } else {
            int nxt = i - 1;
            while (r && p[i].y < b[r].y) {
                update(1, 1, n, b[r].x, nxt, p[i].y - b[r].y);
                nxt = b[r].x - 1;
                --r;
            }
            if (nxt != i - 1) b[++r] = make_pair(nxt + 1, p[i].y);
        }
        a[++l] = make_pair(i, 0);
        b[++r] = make_pair(i, h);
        update(1, 1, n, i, i, h - p[i].x);
        ans = max(ans, p[i + 1].x + mx[1]);
    }
}

int main() {
    cin >> w >> h >> n;
    rep(i, 1, n) scanf("%d%d", &p[i].x, &p[i].y);
    p[++n] = make_pair(0, 0);
    p[++n] = make_pair(w, h);

    solve();

    rep(i, 1, n) swap(p[i].x, p[i].y);  // 翻转
    swap(w, h);
    solve();

    printf("%d\n", ans << 1);
    return 0;
}
```

我发现做题找定量是非常重要的！本题的精髓就在于找定量。上次 usaco 12月月赛 T1 也是找定量，这个真的很有用哇！

（一上来看到矩形肯定头都大啦，哪知道定量是啥啊。。

反正这题 **性质** 和 **定量思想** 缺一不可啊。。。