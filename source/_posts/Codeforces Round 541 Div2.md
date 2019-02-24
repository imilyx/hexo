---
title: 'Codeforces Round 541 Div2'
date: 2019-02-24 21:30:40
tags: 
    - 比赛
mathjax: true
---

[题目链接](http://codeforces.com/contest/1131)

唉，还是有很大的进步空间啊。做了 A 和 C，B 和 D 都调不出来。。

### A. Sea Battle
-----

签到题。

### B. Draw!
-----

是 min(a[i], b[i]) - max(a[i - 1], b[i - 1]) + 1 没错了。。但是要考虑 a[i - 1] = b[i - 1] 的情况！

code :
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 1e4 + 10;
typedef long long ll;
int n;

int main() {
    scanf("%d", &n);
    ll ans = 1;
    ll a = 0, b = 0;
    for (int i = 1; i <= n; i++) {
        ll aa, bb; cin >> aa >> bb;
        if (aa == a && bb == b) continue;
        ans += max(0LL, min(aa, bb) - max(a, b) + 1);
        if (a == b) ans--;
        swap(a, aa), swap(b, bb);
    }
    printf("%lld\n", ans);
    return 0;
}
```

### C. Birthday
-----

说实话没想到居然一下就 A 了(*ˉ︶ˉ*)

记得以前 CF 上见到过 C 题出这种构造方式的，觉得很新奇🤔

给 a[] 排序，然后 i 从 1 开始枚举，每次 += 2。i 和 i + 1 一个放在 ans[] 中 i 位置，一个放在 n - i + 1 的位置，具体放哪要看哪种最优（即使最大差值增加得最慢）。

code :
``` c++
#include <bits/stdc++.h>
using namespace std;

int n, a[105], ans[105], maxn;

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++)
        scanf("%d", &a[i]);
    sort(a + 1, a + n + 1);
    int l = 1, r = n;
    for (int i = 1; i <= n; i += 2) {
        if (l > r) break;
        if (l == r) {
            ans[l] = a[n]; break;
        }
        int x = a[i], y = a[i + 1];
        ans[l] = a[i], ans[r] = a[i + 1];
        int tmp = max(x - ans[l - 1], y - ans[r + 1]);
        if (tmp < maxn) {
            l++, r--;
            continue;
        }
        if (y - ans[l - 1] < tmp && x - ans[r + 1] < tmp)
            swap(ans[l], ans[r]);
        maxn = max(maxn, max(ans[l] - ans[l - 1], ans[r] - ans[r + 1]));
        l++, r--;
    }
    for (int i = 1; i <= n; i++) printf("%d ", ans[i]);
    printf("\n");
    return 0;
}
```

### D. Gourmet choice
-----

一开始是想到了拓扑，但没想到缩点来着，后来越敲越乱，弃疗了。。

没错，正解就是暴力拓扑 + 缩点。缩点其实不可怕，就是并查集啦（此题不用 Tarjan）。aij = '=' 时，将 i 和 j 并一下；aij = '>' 时，建立一条 i 指向 j 的边；'<' 时，建立 j 指向 i 的边。

检查有没有自环（即自己大于自己）和环（a1 > a2 > ... > ak > a1），如果有，输出 No，否则根据拓扑结果输出答案。

code :
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 2010;
int n, m;
char s[N][N];
int fa[N], deg[N], vis[N], len[N];
vector<int> g[N];

int getfa(int x) { return fa[x] == x ? x : fa[x] = getfa(fa[x]); }

void dfs(int u) {
    vis[u] = 1; len[u] = 1;
    for (int i = 0; i < (int)g[u].size(); i++) {
        int v = g[u][i];
        if (v == u) {
            puts("No"); exit(0);
        }
        if (vis[v] == 1) {  // 有环
            puts("No"); exit(0);
        } else if (vis[v] == 2) {
            len[u] = max(len[u], len[v] + 1);
        } else {
            dfs(v);
            len[u] = max(len[u], len[v] + 1);
        }
    }
    vis[u] = 2;
}

int main() {
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n + m; i++) fa[i] = i;
    for (int i = 1; i <= n; i++) scanf("%s", s[i] + 1);

    for (int i = 1; i <= n; i++) for (int j = 1; j <= m; j++)
        if (s[i][j] == '=') fa[getfa(i)] = getfa(j + n);
    
    for (int i = 1; i <= n; i++) for (int j = 1; j <= m; j++) {
        if (s[i][j] == '>') g[getfa(i)].push_back(getfa(j + n)), deg[getfa(j + n)]++;
        else if (s[i][j] == '<') g[getfa(j + n)].push_back(getfa(i)), deg[getfa(i)]++;
    }

    for (int i = 1; i <= n + m; i++) if (!deg[i]) dfs(i);
    for (int i = 1; i <= n + m; i++) if (vis[i] != 2) {
        puts("No"); return 0;
    }
    puts("Yes");
    for (int i = 1; i <= n; i++) printf("%d ", len[getfa(i)]);
    puts("");
    for (int i = 1; i <= m; i++) printf("%d ", len[getfa(i + n)]);
    puts("");
    return 0;
}
```

### E. String Multiplication
-----

首先应该想到 DP：f[i, j] 表示 $p_1 * p_2 * ... * p_i$ 的结果中 j 字母的最长串的长度。

设 $S_i = p_1 * ... * p_i$，最后一定是 $S_{n - 1} * p_n$。按照 pn 分类讨论，一共有三种情况：

设 pn 的开头字符是 c0, 结尾字符是 c1，包含开头的连续段的长度是 pre，包含结尾的连续段的长度是 suf。

1. $c0 \neq c1$，答案可以是三个：(1). 可以是 pn 中最长的连续子段；(2). 如果 $S_{n - 1}$ 中存在 c0，pre + 1；(3). 如果 $S_{n - 1}$ 中存在 c1，suf + 1。

2. c0 = c1, 并且整个串不只有一种字符：如果 $S_{n - 1}$ 中存在 c0，那么答案可以是 pre + suf + 1.

3. 如果 pn 只由一种字符构成，那么求 $S_{n - 1}$ 中最长的字符是 c0 连续段的，设为 t，答案就是 $pre * (t + 1) + t$.

注意记录输出方案。

code :
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 1e5 + 10;
int n;
int f[N][30], pre[30], suf[N];  // pre:前缀 suf:后缀
char s[N];

void solve(int *f) {
    scanf("%s", s + 1);
    int l = strlen(s + 1);
    for (int j = 0; j < 26; j++) {
        int mx = 0;  // p[1] 的最长相同子串长度
        for (int i = 1, cnt = 0; i <= l; i++)
            if (s[i] == j + 'a') mx = max(mx, ++cnt);
            else cnt = 0;
        f[j] = mx, pre[j] = suf[j] = 0;  // 不要忘记置 0
        for (int i = 1; i <= l; i++)
            if (s[i] == j + 'a') ++pre[j];
            else break;
        for (int i = l; i; i--)
            if (s[i] == j + 'a') ++suf[j];
            else break;
    }
}

int main() {
    scanf("%d", &n);
    solve(f[1]);
    for (int i = 2; i <= n; i++) {
        solve(f[i]);
        int l = strlen(s + 1);
        for (int j = 0; j < 26; j++)
            if (f[i - 1][j])
                if (pre[j] != l) // 判断 pi 是否全由 j 组成
                    f[i][j] = max(f[i][j], pre[j] + suf[j] + 1);
                else f[i][j] = max(f[i][j], l * (f[i - 1][j] + 1) + f[i - 1][j]);
    }
    int ans = 0;
    for (int i = 0; i < 26; i++) ans = max(ans, f[n][i]);
    printf("%d\n", ans);
    return 0;
}
```

### F. Asya And Kittens
-----

这道看很多人一开始就去 A 了呀，果然比 E 题简单 🤔 并查集模拟就好啦。

对于每次的 (x, y), “并” 其实是并 x 所在的块的最右边一个和 y 所在的块的最左边一个，就像真的合并一样。记录某个块的最右端和最左端就可以了。

code :
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 150010;
int n, d[N], pre[N], nxt[N], fa[N];
vector<int> g[N];

void add(int u, int v) {
    g[u].push_back(v), g[v].push_back(u);
    d[u]++, d[v]++;
}

int getfa(int x) { return fa[x] == x ? x : fa[x] = getfa(fa[x]); }

void print(int x, int fa) {
    printf("%d ", x);
    for (int i = 0; i < (int)g[x].size(); i++)
        if (g[x][i] != fa) print(g[x][i], x);
}

int main() {
    scanf("%d", &n);;
    for (int i = 1; i <= n; i++) pre[i] = nxt[i] = fa[i] = i;
    for (int i = 1; i < n; i++) {
        int x, y; scanf("%d%d", &x, &y);
        x = getfa(x), y = getfa(y);
        add(nxt[x], pre[y]);
        nxt[x] = nxt[y];
        fa[y] = x;
    }
    int root = 0;
    for (int i = 1; i <= n; i++) if (d[i] == 1) {
        root = i; break;
    }
    print(root, 0);
    return 0;
}
```

要加油！！