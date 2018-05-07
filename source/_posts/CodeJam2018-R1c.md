---
title: CodeJam2018-R1c
date: 2018-05-07 22:26:40
tags:
---

### A. A Whole New Word
-----------------------

https://codejam.withgoogle.com/2018/challenges/0000000000007765/dashboard

set记题目给定的字符串出现过，然后dfs枚举字符串，26^L，在找到第一个合法的字符串后立刻返回。

CODE：
``` c++
#include <bits/stdc++.h>
using namespace std;

set<string> h;
int c[26][26];
string ret;
int n, L;

bool dfs(int x) {
    if (x >= L) {
        if (h.count(ret)) return false;
        cout << ret << endl;
        return true;
    }
    for (int i = 0; i < 26; ++i) if (c[x][i]) {
        ret[x] = 'A' + i;
        if (dfs(x + 1)) return true;
    }
    return false;
}

int kase;
void solve() {
    cin >> n >> L;
    h.clear();
    memset(c, 0, sizeof(c));
    for (int i = 0; i < n; ++i) {
        string s;
        cin >> s;
        for (int j = 0; j < L; ++j) c[j][s[j] - 'A'] = 1;
        h.insert(s);
    }
    ret = "";
    for (int j = 0; j < L; ++j) ret += '0';
    cout << "Case #" << ++kase << ": ";
    if (!dfs(0)) cout << "-" << endl;
}

int main() {
    int T;
    scanf("%d", &T);
    while (T--) solve();
    return 0;
}
```



### B. Lollipop Shop
--------------------

https://codejam.withgoogle.com/2018/challenges/0000000000007765/dashboard

这是一道交互题，给出每种棒棒糖被喜欢的概率，要求最后答案是正解的90%以上。
乍一看很不可做。不知道大家喜欢的糖，怎么做呢？
贪心思想，将概率最小的糖给他。/不是概率大的：概率大就有很多人喜欢了，怎么给呢？
cnt[]记录概率，若概率相同，怎么办呢？
概率性的，当然少不了随机啦！/随机大法好
在这种各为50%的情况下，我们随一个数，通过奇偶性来决定分给两者中哪一位。
最后 fflush(stdout) 刷新输入。
这个算法的概率性体现在“随一个数，判断奇偶性”中，如果不是交互题，就不存在概率的问题，就是正确的算法。

CODE：
``` c++
#include <bits/stdc++.h>
using namespace std;

int cnt[205], mk[205], biu[205];
void solve() {
    int n;
    cin >> n;
    memset(mk, 0, sizeof(mk));
    memset(cnt, 0, sizeof(cnt));
    cnt[0] = 233;
    for (int i = 1; i <= n; ++i) {
        int K;
        cin >> K;
        memset(biu, 0, sizeof(biu));
        for (int j = 1; j <= K; ++j) {
            int x;
            cin >> x;
            ++cnt[x + 1], biu[x + 1] = 1;
        }
        int p = 0;
        for (int j = 1; j <= n; ++j) if (biu[j] && !mk[j]) {
            if (cnt[j] < cnt[p]) p = j;
            else if (cnt[j] == cnt[p] && (rand() & 1)) p = j;
        }
        if (!p) cout << -1 << endl;
        else cout << p - 1 << endl, mk[p] = 1;
        fflush(stdout);
    }
}

int main () {
    srand(time(0) * clock());
    int T;
    cin >> T;
    while (T--) solve();
    return 0;
}
```



### C. Ant Stack
----------------

https://codejam.withgoogle.com/2018/challenges/0000000000007765/dashboard/000000000003e0a8

很显然哇，是LIS的改编，朴素的LIS，Task1可以过。
Task2呢？
之前我们想到的是表示长度，但重量那一维太大了，我们将长度与重量换一换。
f[i][j] 表示，前 i 个中，
可以发现，a = 6b 时，a + b = 7b / 6。又因保证 wi <= 10 ^ 9，可以发现 (7 / 6) ^ 150 > 10 ^ 9，因此最多150人。那么我们1～150循环枚举长度。发现每一次循环，DP只需要上一次DP的消息，所以我们使用滚动数组。

CODE：
``` c++
#include <bits/stdc++.h>
using namespace std;
typedef long long LL;

const int maxn = (int)(1e5) + 5;
const LL inf = (LL)(1e18) + 100;
LL f[2][maxn], a[maxn];

int kase;
void solve() {
    int n;
    cin >> n;
    for (int i = 1; i <= n; ++i) cin >> a[i];
    int now = 0, lst = 0, mx = 0;
    for (int j = 0; j <= n; ++j) f[0][j] = f[1][j] = inf;
    f[now][0] = 0;
    for (int j = 1; j <= 150; ++j) {
        now = lst ^ 1;
        for (int i = 0; i <= n; ++i)  f[now][i] = inf;
        LL ww = f[lst][0];
        for (int i = 1; i <= n; ++i) {
            if (ww <= a[i] * 6) f[now][i] = ww + a[i];
            ww = min(ww, f[lst][i]);
        }
        for (int i = 0; i <= n; ++i)
            if (f[now][i] < inf) mx = j;
        lst = now;
    }
    cout << "Case #" << ++kase << ": " << mx << endl;
}

int main() {
    ios::sync_with_stdio(0);
    int T;
    cin >> T;
    while (T--) solve();
    return 0;
}
```