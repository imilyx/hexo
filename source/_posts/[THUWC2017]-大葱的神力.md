---
title: '[THUWC2017]-大葱的神力'
date: 2020-12-22 12:50:40
tags: 
    - 提交答案
mathjax: true
---

玩玩提答！qwq。看到一句话：提答的本质是模拟退火和找规律。哦这样吗！那我去学模拟退火（

```
模拟退火算法流程（贺）：
随机变化坐标，幅度为 T
计算新解与当前解的差 delta。
以最大化答案为例，若 delta > 0 则用新解替换当前解；否则以 exp(-delta / T) 的概率用新解替换当前解。
温度乘上一个小于 1 的系数，即降温。
随着温度不断降低，变化幅度也不断减小，接受一个更劣解的概率也越来越小。
```

调参看脸。有人说可以观察答案的变化率什么的，总之是个完全玄学的东西。

好，上正文！（其实是想存代码，好有成就感的说 qwq

每个 case 都有特性，需要观察找规律。

### Case 1 & 2

暴搜 + （我的垃圾）剪枝

``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

int n, m;
int a[30], b[30], mp[30][30], sum, to[30], ans, pos[30];
int used[30], mx[30];

void dfs(int x) {
    if (sum + mx[x] < ans) {
        return;
    }
    if (x == n + 1) {
        if (sum > ans) {
            rep(i, 1, n) {
                pos[i] = to[i];
            } ans = sum;
        }
        return;
    }
    dfs(x + 1);
    rep(y, 1, m) {
        if (used[y] + a[x] <= b[y]) {
            sum += mp[x][y];
            to[x] = y;
            used[y] += a[x];
            dfs(x + 1);
            sum -= mp[x][y];
            used[y] -= a[x];
            to[x] = 0;
        }
    }
}

int main() {
    // freopen("drawer2.in", "r", stdin);
    // freopen("drawer2.out", "w", stdout);

    cin >> n >> m;
    rep(i, 1, n) {
        cin >> a[i];
    }
    rep(i, 1, m) {
        cin >> b[i];
    }
    rep(i, 1, n) {
        rep(j, 1, m) {
            cin >> mp[i][j];
            mx[i] = max(mx[i], mp[i][j]);
        }
    }
    for (int i = n; i; --i) mx[i] += mx[i + 1];
    dfs(1);
    rep(i, 1, n)
        printf("%d ", pos[i]); puts("");
    return 0;
}
```

### Case 3

抽屉唯一，跑背包。

``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 10005, M = 2e3 + 5;
int n, m, a[M], w[M], f[M][N], lst[M][N], ans[M];

void work(int x, int y) {
    if (!x) return;
    if (y ^ lst[x][y]) ans[x] = 1;
    work(x - 1, lst[x][y]);
}

int main() {
    cin >> n >> m;
    rep(i, 1, n) {
        scanf("%d", &a[i]);
    }
    int tmp; cin >> tmp;
    memset(f, ~0x3f, sizeof(f));
    f[0][0] = 0;
    rep(i, 1, n) {
        scanf("%d", &w[i]);
        rep(j, 0, 10000) {
            f[i][j] = f[i - 1][j], lst[i][j] = j;
            if (j >= a[i]) {
                if (f[i][j] < f[i - 1][j - a[i]] + w[i]) {
                    f[i][j] = f[i - 1][j - a[i]] + w[i];
                    lst[i][j] = j - a[i];
                }
            }
        }
    }
    int id = 0;
    rep(i, 0, 10000) if (f[n][i] > f[n][id]) {
        id = i;
    }
    work(n, id);
    rep(i, 1, n) printf("%d ", ans[i]); puts("");
    return 0;
}
```

### Case 4 & 5 & 6

大葱体积唯一且抽屉体积是大葱体积倍数——裸的费用流。

值得一提的是有个 Case 大葱体积都在 ~~19260817~~19900000 左右，可视为微小的扰动，不会影响最终答案，取最大值作为体积就好。

``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 1e3 + 5, M = 205, E = N * M * 2 + N + M, inf = 0x3f3f3f3f;
int S, T, n, m;
int a[N], b[M], mp[N][N], ans[N];
int fr[E], to[E], nxt[E], lnk[N * 2], val[E], cap[E], cnt = 1;
int dis[N * 2], pre[N * 2], inq[N * 2], rest[N * 2];
queue<int> q;

void add(int x, int y, int c, int w) {
    fr[++cnt] = x, to[cnt] = y, nxt[cnt] = lnk[x], lnk[x] = cnt, val[cnt] = w, cap[cnt] = c;
}

bool spfa(int S, int T, int &flow, int &cost) {
    memset(dis, 0x3f, sizeof(dis));
    memset(pre, 0, sizeof(pre));
    memset(rest, 0x3f, sizeof(rest));
    memset(inq, 0, sizeof(inq));
    dis[S] = 0, inq[S] = 1, rest[S] = inf, pre[S] = 0;
    q.push(S);
    while (q.size()) {
        int x = q.front(); q.pop(); inq[x] = 0;
        for (int i = lnk[x]; i; i = nxt[i]) {
            int y = to[i];
            if (cap[i] && dis[y] > dis[x] + val[i]) {
                dis[y] = dis[x] + val[i];
                pre[y] = i;
                rest[y] = min(rest[x], cap[i]);
                if (!inq[y])
                    inq[y] = 1, q.push(y);
            }
        }
    }
    if (dis[T] > 1e9)
        return 0;
    flow += rest[T];
    cost += rest[T] * dis[T];
    int u = T;
    while (u != S) {
        cap[pre[u]] -= rest[T];
        cap[pre[u] ^ 1] += rest[T];
        u = fr[pre[u]];
    }
    return 1;
}

void Mcmf() {
    int flow = 0, cost = 0;
    while (spfa(S, T, flow, cost));
}

int main() {
    cin >> n >> m;
    S = 0, T = n + m + 1;
    int v = 0;
    rep(i, 1, n) {
        scanf("%d", &a[i]);
        v = max(v, a[i]);
    }
    rep(i, 1, n) {
        add(S, i, 1, 0), add(i, S, 0, 0);
    }
    rep(i, 1, m) {
        scanf("%d", &b[i]);
        add(i + n, T, b[i] / v, 0), add(T, i + n, 0, 0);
    }
    rep(i, 1, n) {
        rep(j, 1, m) {
            scanf("%d", &mp[i][j]);
            add(i, j + n, 1, -mp[i][j]), add(j + n, i, 0, mp[i][j]);
        }
    }
    Mcmf();
    rep(x, 1, n) {
        for (int i = lnk[x]; i; i = nxt[i]) {
            int y = to[i];
            if (!y) continue;
            if (!cap[i]) {
                ans[x] = y - n; break;
            }
        }
        printf("%d ", ans[x]);
    }
    puts("");
    return 0;
}
```

### Case 7

只有第一个大葱体积不同，枚举它放在哪，跑多次费用流。跑了一分多钟，反正是提答 ~

``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
using namespace std;

const int N = 505, M = 205, E = N * M * 2 + N + M, inf = 0x3f3f3f3f;
int S, T, n, m, Cost, V;
int a[N], b[M], mp[N][N], ans[N];
int fr[E], to[E], nxt[E], lnk[N * 2], val[E], cap[E], cnt = 1;
int dis[N * 2], pre[N * 2], inq[N * 2], rest[N * 2];
queue<int> q;

void add(int x, int y, int c, int w) {
    fr[++cnt] = x, to[cnt] = y, nxt[cnt] = lnk[x], lnk[x] = cnt, val[cnt] = w, cap[cnt] = c;
}

bool spfa(int S, int T, int &flow, int &cost) {
    rep(i, S, T) {
        dis[i] = inf;
        pre[i] = 0;
        inq[i] = 0;
    }
    dis[S] = 0, inq[S] = 1, rest[S] = inf, pre[S] = 0;
    q.push(S);
    while (q.size()) {
        int x = q.front(); q.pop(); inq[x] = 0;
        for (int i = lnk[x]; i; i = nxt[i]) {
            int y = to[i];
            if (cap[i] && dis[y] > dis[x] + val[i]) {
                dis[y] = dis[x] + val[i];
                pre[y] = i;
                rest[y] = min(rest[x], cap[i]);
                if (!inq[y])
                    inq[y] = 1, q.push(y);
            }
        }
    }
    if (dis[T] > 1e9)
        return 0;
    flow += rest[T];
    cost += rest[T] * dis[T];
    int u = T;
    while (u != S) {
        cap[pre[u]] -= rest[T];
        cap[pre[u] ^ 1] += rest[T];
        u = fr[pre[u]];
    }
    return 1;
}

int Mcmf() {
    int flow = 0, cost = 0;
    while (spfa(S, T, flow, cost));
    return cost;
}

int main() {
    cin >> n >> m;
    S = 0, T = n + m + 1;
    rep(i, 1, n) {
        scanf("%d", &a[i]);
    }
    V = a[2];
    rep(i, 1, m) {
        scanf("%d", &b[i]);
    }
    rep(i, 1, n) {
        rep(j, 1, m) {
            scanf("%d", &mp[i][j]);
        }
    }
    b[48] -= a[1];
    rep(i, 2, n) {
        add(S, i, 1, 0), add(i, S, 0, 0);
    }
    rep(i, 1, m) {
        add(i + n, T, b[i] / V, 0), add(T, i + n, 0, 0);
    }
    rep(i, 2, n) {
        rep(j ,1, m) {
            add(i, j + n, 1, -mp[i][j]), add(j + n, i, 0, mp[i][j]);
        }
    }
    int cost = Mcmf() - mp[1][48];
    ans[1] = 48;
    rep(x, 2, n) {
        ans[x] = 0;
        for (int i = lnk[x]; i; i = nxt[i]) {
            int y = to[i];
            if (!y) continue;
            if (!cap[i]) {
                ans[x] = y - n; break;
            }
        }
    }
    rep(x, 1, n) printf("%d ", ans[x]);
    puts("");
    return 0;
}
```

### Case 8 & 9 & 10

没有任何规律。于是就上模拟退火（学以致用

我是 rand 排列，随便调参，最优解只有 51 万左右，然而其他人都是 60 万左右，心慌慌

交一发竟然也是 4 + 2 + 1，开心。

``` c++
#include <bits/stdc++.h>
#define rep(i, x, y) for (int i = x; i <= y; i++)
#define db double
using namespace std;

typedef long long ll;
const int N = 1e3 + 10, M = 305;
const db down = 0.986;
int n, m;
int a[N], b[M], mp[N][M], to[N], idb[N], ida[N];
int used[N], tmp[N];
ll ans;

void work() {
    db T = 30000;
    while (T > 1e-15) {
        random_shuffle(ida + 1, ida + n + 1);
        random_shuffle(idb + 1, idb + m + 1);
        rep(i, 1, n) tmp[i] = 0;
        rep(i, 1, m) used[i] = 0;
        int cur = 1;
        ll sum = 0;
        rep(i, 1, n) {
            if (used[idb[cur]] + a[ida[i]] <= b[idb[cur]]) {
                used[idb[cur]] += a[ida[i]];
                tmp[ida[i]] = idb[cur];
                sum += mp[ida[i]][idb[cur]];
            } else {
                ++cur; --i;
                if (cur > m) break;
            }
        }
        ll del = sum - ans;
        if (del > 0) {
            ans = sum;
            rep(i, 1, n) to[i] = tmp[i];
        } else {
            if (exp(-del / T) * RAND_MAX > rand()) {
                ans = sum;
                rep(i, 1, n) to[i] = tmp[i];
            }
        }
        T *= down;
    }
}

void solve() {
    rep(o, 1, 5) {
        rep(i, 1, n) ida[i] = idb[i] = i;
        work();
    }
}

int main() {
    srand(time(0) * clock() % 114514);
    cin >> n >> m;
    rep(i, 1, n) {
        scanf("%d", &a[i]);
    }
    rep(i, 1, m) {
        scanf("%d", &b[i]);
    }
    rep(i, 1, n) {
        rep(j, 1, m) {
            scanf("%d", &mp[i][j]);
        }
    }
    solve();
    printf("%lld :\n", ans);
    rep(i, 1, n) {
        printf("%d ", to[i]);
    }
    puts("");
    return 0;
}
```

[评测记录](https://loj.ac/s/1017355)

$10 * 7 + 4 + 2 + 1 = 77$，海星