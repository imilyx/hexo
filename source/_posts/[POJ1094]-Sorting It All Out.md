---
title: '[POJ1094]-Sorting It All Out'
date: 2018-07-04 14:50:40
tags: 
    - 最短路
mathjax: true
---

https://vjudge.net/problem/POJ-1094

题意：给定一组字母的大小关系，判断能否组成唯一的拓扑序列。输出格式有三种形式：
(1) 该字母序列有序，并依次输出；
(2) 该序列不能判断是否有序；
(3) 该序列字母次序之间有矛盾，即有环存在。

这道题我们用 Floyd 的传递闭包的性质完成。若是 (1)，那么二分关系数。还有两个坑：

1. 同时矛盾与不确定，优先矛盾
2. 若发现有唯一解，直接输出，不要管后面会不会矛盾。

CODE：
``` c++
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <iostream>
using namespace std;

int n, m, d[30][30], ans;

struct node {
    int x, y;
}opt[1010], sum[30];

bool cmp(node a, node b) {return a.y > b.y;}

void send() {  // Floyd传递闭包
    for (int k = 1; k <= n; k++)
        for (int i = 1; i <= n; i++)
            for (int j = 1; j <= n; j++)
                d[i][j] |= d[i][k] & d[k][j];
}

void solve() {
    printf("Sorted sequence determined after %d relations: ", ans);
    for (int i = 1; i <= n; i++) {
        sum[i].x = i, sum[i].y = 0;
        for (int j = 1; j <= n; j++)
            if (d[i][j] == 1) sum[i].y++;  // 拓扑排序
    }
    sort(sum + 1, sum + n + 1, cmp);
    for (int i = 1; i <= n; i++) printf("%c", 'A' - 1 + sum[i].x);
    printf(".\n");
}

int check() {
    bool incon = false, cannot = false;
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= n; j++) {
            if (i == j && d[i][i] == 0) continue;
            if (d[i][j] == d[j][i] && d[i][j] == 1) incon = true;
            if (d[i][j] == d[j][i] && d[i][j] == 0) cannot = true;  // 存在没有讲清的关系自然无法判断序列了！
        }
    if (incon) return -1;
    if (cannot) return 0;
    return 1;
}

int main() {
    while (true) {
        scanf("%d%d", &n, &m);
        if (!n && !m) break;
        memset(d, 0, sizeof(d));
        for (int i = 1; i <= m; i++) {
            char ch[5];
            scanf("%s", ch);
            opt[i].x = ch[0] - 'A' + 1;
            opt[i].y = ch[2] - 'A' + 1;
            d[opt[i].x][opt[i].y] = 1;
        }
        send();
        int flag = check();
        if (flag == 0) {
            printf("Sorted sequence cannot be determined.\n");
            continue;
        }
        int l = 1, r = m;
        if (flag == 1) {
            while (l <= r) {
                int mid = (l + r) >> 1;
                memset(d, 0, sizeof(d));
                for (int i = 1; i <= mid; i++) d[opt[i].x][opt[i].y] = 1;
                send();
                if (check() == 1) r = mid - 1, ans = mid;
                else
                    l = mid + 1;
            }
            solve();
            continue;
        } else if (flag == -1) {
            bool ATP = false;
            while (l <= r) {
                int mid = (l + r) >> 1;
                memset(d, 0, sizeof(d));
                for (int i = 1; i <= mid; i++) d[opt[i].x][opt[i].y] = 1;
                send();
                int tmp = check();
                if (tmp != 0) {
                    ans = mid, r = mid - 1;
                    if (tmp == 1) ATP = true;
                } else {
                    l = mid + 1;
                }
            }
            if (!ATP) printf("Inconsistency found after %d relations.\n", ans);
            else {
                solve();
                continue;
            }
        }
    }
    return 0;
}
```