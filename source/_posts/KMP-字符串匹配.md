---
title: KMP-字符串匹配
date: 2018-04-22 21:38:40
tags:
---

今天来学KMP算法啦！
KMP是用来做字符串匹配的，比如，给定S序列：abababa， T序列：ba，
问T序列在S序列里出现的位置，如题则为3、5、7。

那么，有一种暴力算法，枚举结尾，O(nm)，当然仅限于暴力。。
KMP是改进版，简洁好打，O(n + m)!!
朴素算法中有许多不必要的枚举，如果失配了，会回归原点，从下一位再次开始；
而KMP充分利用匹配失败的信息，尽量减少匹配次数。

算法中涉及到一个next数组，这也是KMP中最重要的部分。
next[i]表示前i位中，前缀与后缀相同的最长部分，
比如，序列abcab，next[5] = 2('ab'), next[4] = 1('a')。
这样，利用next[]，一个序列中后缀匹配不上，就用前缀去匹配。

CODE：
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 100005;
char s[N], t[N];
int next[N];  // 前缀与后缀相同的最大长度

int main() {
    scanf("%s%s", s + 1, t + 1);
    int n = strlen(s + 1), m = strlen(t + 1);
    for (int i = 2, k = 0; i <= n; i++) {
        while (k && t[i] != t[k + 1]) k = next[k];  // 跳回去，往前回溯
        if (t[i] == t[k + 1]) k++;  // 相同的情况下
        next[i] = k;
    }  // 先处理next[]
    for (int i = 1, k = 0; i <= n; i++) {
        while (k && s[i] != t[k + 1]) k = next[k];
        if (s[i] == t[k + 1]) k++;
        if (k == m) printf("%d ", i);
    }
    putchar('\n');
    return 0;
}
```

感觉KMP充分将时间用在刀刃上了。。好妙的KMP。。