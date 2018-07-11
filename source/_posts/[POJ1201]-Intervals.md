---
title: '[POJ1201]-Intervals'
date: 2018-07-11 22:56:40
tags: 
    - 差分约束
mathjax: true
---

https://vjudge.net/problem/POJ-1201

题意：从 0 ～ 50000 中选出尽量少的整数，使每个区间 $[a_i, b_i]$ 内都有至少 $c_i$ 个数被选。

分析条件：
设 $s[k]$ 表示 0 ～ k 之间最少选出多少个数，那么：

$$s[b_i] - s[a_i - 1] \geq c_i$$

超级明显的差分约束系统？

那么，我们还要创造一些初始化的条件，可以根据题目中隐含的条件来：

1. $s[k] - s[k - 1] \geq 0$
2. $s[k] - s[k - 1] \leq 1$

因此，我们把 -1 ～ 50000 这 50002 个整数分别作为图中的节点，从每个 k - 1 到 k 连长度为 0 的有向边， k 到 k - 1 连长度为 -1 的有向边，从每个 $a_i - 1$ 到 $b_i$ 连长度为 $c_i$ 的有向边。

当然，我们也可以取 $a_i - 1$ 的最小值 Min，$b_i$ 的最大值 Max，然后从 Min 到 Max 连初始边。求完后，s[Max] 即为答案。

不过，既然与区间有关，应该也可以用贪心求解，并用数据结构进行优化，可以考虑一下。

CODE：
``` c++
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <queue>
using namespace std;

const int N = 5e4 + 5, inf = 1e9 + 7;
int n, dis[N], inq[N], Min = inf, Max = -inf;
int to[N << 1], nxt[N << 1], val[N << 1], lnk[N], cnt;
queue<int> q;

void add(int u, int v, int w) {
    to[++cnt] = v, nxt[cnt] = lnk[u], lnk[u] = cnt, val[cnt] = w;
}

void spfa() {
    for (int i = Min; i <= Max; i++) dis[i] = -inf;
    dis[Min] = 0;
    inq[Min] = 0;
    q.push(Min);
    while (!q.empty()) {
        int u = q.front();
        q.pop();
        inq[u] = 0;
        for (int i = lnk[u]; i; i = nxt[i]) {
            int y = to[i];
            if (dis[y] < dis[u] + val[i]) {
                dis[y] = dis[u] + val[i];
                if (!inq[y]) {
                    q.push(y);
                    inq[y] = 1;
                }
            }
        }
    }
}

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) {
        int a, b, c;
        scanf("%d%d%d", &a, &b, &c);
        add(a, b + 1, c);
        Min = min(Min, a);
        Max = max(Max, b + 1);
    }
    for (int i = Min; i < Max; i++) {
        add(i, i + 1, 0);
        add(i + 1, i, -1);
    }
    spfa();
    printf("%d\n", dis[Max]);
    return 0;
}
```