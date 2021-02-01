---
title: '[CF913C]-Party Lemonade'
date: 2018-09-18 23:21:40
tags: 
    - 贪心
mathjax: true
hidden: true
---

[题目链接](http://codeforces.com/problemset/problem/913/C)

题意：有 n 种饮料，第 i 种饮料的体积是 $2^{i - 1}$，花费是 $c_i$，求饮料体积不小于 L 的最小花费。

看到 2 的幂次方，首先就应该想到二进制啦(●--●)

可以先处理出购买体积为 $2^i$ 的饮料最小花费，即 $c[i + 1] = min\{c[i + 1], 2 * c[i]\}$. 然后可以按位枚举。注意，题目中说是体积不小于L的，所以从后往前枚举每位，可以看代码，该份代码刚好保证 sum 只计算 need = 1 的位的价值之和，同时又考虑了题例2的情况，非常妙！

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;
int n;
ll L, c[35], sum, ans = 1e18;

int main() {
	cin >> n >> L;
	for (int i = 0; i < n; i++) cin >> c[i];
	for (int i = 0; i < n - 1; i++)
		c[i + 1] = min(c[i] * 2, c[i + 1]);
	for (int i = n - 1; i >= 0; i--) {  // 从后往前哦
		int need = L >> i;
		sum += need * c[i];
		L -= need << i;
		ans = min(ans, sum + (L > 0) * c[i]);  // 并不是直接 ans = sum! 并不要求只有 need = 1，i 才有可能被购买，题例 2 就是很好的例子
	}
	cout << ans << endl;
	return 0;
}
```