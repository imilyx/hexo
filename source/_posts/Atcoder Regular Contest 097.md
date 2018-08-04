---
title: 'Atcoder Regular Contest 097'
date: 2018-08-04 21:39:40
tags: 
    - 比赛
mathjax: true
---

### C. K-th Substring
-----

数据很小，暴力即可。

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 5010;
string str[N * 6];
char s[N];
int n, k, cnt;

int main() {
    ios::sync_with_stdio(false);
    cin >> s >> k;
    n = strlen(s);
    for (int i = 0, T; i < n; i++) {
        str[++cnt] = s[i];
        T = min(k, n - i);  // 这里很重要！没有就会TLE！
        for (int j = 1; j < T; j++, cnt++) str[cnt + 1] = str[cnt] + s[i + j];
    }
    sort(str + 1, str + cnt + 1);
    unique(str + 1, str + cnt + 1);
    cout << str[k] << endl;
    return 0;
}
```

### D. Equals
-----

用并查集维护：$x_j$ 和 $y_j$ 是在同一连通块里的。

若 i 和 p[i] 在同一连通块里，那么 ans + 1，即同一连通块里的元素是可以随意调换位置的。

code:
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 1e5 + 5;
int n, m, p[N], fa[N], ans;

int getfa(int x) {return fa[x] == x ? x : fa[x] = getfa(fa[x]);}

int main() {
    ios::sync_with_stdio(false);
    cin >> n >> m;
    for (int i = 1; i <= n; i++) fa[i] = i;
    for (int i = 1; i <= n; i++) cin >> p[i];
    for (int i = 1; i <= m; i++) {
        int x, y;
        cin >> x >> y;
        int fx = getfa(x), fy = getfa(y);
        if (fx != fy) fa[fx] = fy;
    }
    for (int i = 1; i <= n; i++)
        if (getfa(i) == getfa(p[i])) ans++;
    cout << ans << endl;
    return 0;
}
```