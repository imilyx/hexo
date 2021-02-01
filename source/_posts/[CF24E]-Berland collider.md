---
title: '[CF24E]-Berland collider'
date: 2018-04-27 21:24:40
tags:
    - 二分
hidden: true
---

http://codeforces.com/problemset/problem/24/E

题目大意:在x轴上有n个点，每个点向左或向右发射子弹，给定每个点的坐标xi和所发射子弹的飞行速度vi，问所有相向而行的的子弹中最短的相遇时间。
输入:第一行一个整数n，之后n行每行两个整数xi,vi（以向右为正方向），表示每个子弹的初始坐标和速度。 输出:如果不会有子弹相遇，输出-1，否则输出一个双浮点数，要求与标准答案相差不超过10^-9。

n <= 5 * 10 ^ 5, 所以只能用O(nlogn)的算法了，又涉及到最值问题，所以可以尝试用二分答案。
二分什么呢？最短的相遇时间(t)。
二分复杂度是logn，check函数就只能写O(n)的啦。
维护向右运动的物体的最大最终坐标r(x[i] + v[i] * t)，如果遇到向左运动的物体，那么也计算它的最终坐标l，若r > l，则说明两者会相遇，更新答案，再往更小的答案二分。

CODE:
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 500005;
const double eps = 1e-12;
int n, x[N], v[N], flag;

int calc(double a) {return (a < -eps ? -1 : (a < eps ? 0 : 1));}

bool check(double t) {
    double d = -1e20, tmp;
    for (int i = 1; i <= n; i++) {
        tmp = x[i] + v[i] * t;
        if (v[i] > 0) d = max(d, tmp);
        else if (tmp <= d) return true;
    }
    return false;
}

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) {
        scanf("%d%d", &x[i], &v[i]);
        if (flag == 0 && v[i] > 0) flag = 1;
        else if (flag == 1 && v[i] < 0) flag = 2;
    }
    if (flag != 2) {
        printf("-1\n");
        return 0;
    }
    double l = 0, r = (double)1e9;
    int t = 0;
    while (calc(r - l) > 0 && t < 1000) {
        t++;
        double mid = (l + r) / 2;
        if (check(mid)) r = mid;
        else l = mid;
    }
    printf("%.10lf\n", r);
    return 0;
}
```

自己想的时候，一直跳不出n^2的方法，但是可以发现，n^2每次计算两个物体相遇的时间，实际上等于重复计算了两个的飞行路程。
分别记录一个最右坐标和一个最左坐标，相当于是向右一族和向左一族里最远的，若它俩都不能相遇，那么其它的也不可能了，这个t也不符合，返回值为false。
我们check也只是想知道在t的时间内有无物体相遇，或者说只是想得到符合或不符合的答复。