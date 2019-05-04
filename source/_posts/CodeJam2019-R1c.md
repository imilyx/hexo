---
title: CodeJam2019-R1c
date: 2019-05-04 20:59:40
tags:
    - 比赛
---

### A. Robot Programming Strategy
-----

分类讨论每一位 pos：

如果 pos 这位，只有一种字符，那么显然有解；

如果有三种字符，不管答案串当前位为什么，都会被某种字符克制，显然无解；

如果有两种字符，那么当前只有一种选择，即：打平一种字符，赢过另一种字符。

dfs！

code :
``` c++
#include <bits/stdc++.h>
using namespace std;

const int N = 505;
int T, n, len[N], S[505], a[505], L;
string c[N];

char calc(int i, int pos) { return c[i][pos % len[i]]; }

int dfs(int pos) {
    if (pos == 500) return 0;  // 莫忘
    L++;
    int cnt[3];
    memset(cnt, 0, sizeof(cnt));
    for (int i = 1; i <= n; i++) if (S[i]) {
        if (calc(i, pos) == 'R') cnt[0]++;
        if (calc(i, pos) == 'S') cnt[1]++;
        if (calc(i, pos) == 'P') cnt[2]++;
    }
    int num = 0;
    for (int i = 0; i < 3; i++) if (cnt[i]) num++;
    if (num == 3) return 0;
    if (num == 1) {
        for (int i = 0; i < 3; i++) if (cnt[i]) {
            a[pos] = (i + 2) % 3;
            return 1;
        }
    }
    for (int i = 0; i < 3; i++)
        if (cnt[i] && cnt[(i + 1) % 3]) {
            a[pos] = i;
            int k = (i + 1) % 3;
            for (int j = 1; j <= n; j++) {
                if (calc(j, pos) == 'R' && !k) S[j] = 0;
                if (calc(j, pos) == 'S' && k == 1) S[j] = 0;
                if (calc(j, pos) == 'P' && k == 2) S[j] = 0;
            }
            return dfs(pos + 1);
        }
}

int main() {
    scanf("%d", &T);
    for (int cas = 1; cas <= T; cas++) {
        L = 0;
        scanf("%d", &n);
        for (int i = 1; i <= n; i++) S[i] = 1;
        for (int i = 1; i <= n; i++) {
            cin >> c[i];
            len[i] = c[i].length();
        }
        printf("Case #%d: ", cas);
        if (!dfs(0)) puts("IMPOSSIBLE");
        else {
            for (int i = 0; i < L; i++)
                printf("%c", a[i] == 0 ? 'R' : (a[i] == 1 ? 'S' : 'P'));
            puts("");
        }
    }
    return 0;
}
```

### B. Power Arrangers
-----

是交互题！

总共 595 个数字。分三轮。

1. 询问 119 个序列的首位，我们发现 A B C D E 的数量必定有一个是比别人少一的，那么答案序列的首位就是它。

2. 询问 23（23 = (119 + 1) / 5 - 1）个序列的次位，bla...bla...bla

3. 询问 5（5 = (23 + 1) / 4 - 1）个序列的第三位，bla...bla...bla

4. 询问 1 (1 = (5 + 1) / 3 - 1) 个。。。诶？只有一个了？那么直接询问它的第四位，顺便也能得出第五位了！

这边有个易错点：假设询问的第四位是 C，第五位是 D，这个 * * * C D 序列是还存在的，没有被删除的！所以答案序列是 * * * D C! 这个一定要注意

然后我们算一发... 119 + 23 + 5 + 1 + 1 = 149 ！离 150 次只有一步之遥... 卡的好紧...

code :
``` c++
#include <bits/stdc++.h>
using namespace std;

int T, F;
int a[700], ans[10], cnt[10];
char t[5];

int _count;

int getn(int p) {
    _count ++;
    printf("%d\n", p);
    fflush(stdout);
    scanf("%s", t);
    return t[0] - 'A';
}

int main() {
    scanf("%d%d", &T, &F);
    for (int cas = 1; cas <= T; cas++) {
        _count = 0;
        for (int i = 1; i <= 3; i++) {
            memset(cnt, 0, sizeof(cnt));
            for (int j = 1; j <= 119; j++) {
                bool ff = 1;
                for (int k = 1; k < i; k++) {
                    if (a[(j - 1) * 5 + k] != ans[k]) {
                        ff = 0; break;
                    }
                }
                if (ff) {
                    int p = (j - 1) * 5 + i;
                    a[p] = getn(p);
                    cnt[a[p]]++;
                }
            }
            int id = 0;
            for (int j = 1; j < 5; j++)
                if (!cnt[id]) id = j;
                else if (cnt[id] > cnt[j] && cnt[j]) id = j;
            // fprintf(stderr, "%d\n", cnt[id]);
            ans[i] = id;
        }
        for (int i = 1; i <= 119; i++) {
            int p = (i - 1) * 5;
            bool ff = 1;
            for (int j = 1; j <= 3; j++) {
                if (a[p + j] != ans[j]) {
                    ff = 0; break;
                }
            }
            if (ff == 1) {
                ans[5] = getn(p + 4);
                ans[4] = getn(p + 5);
                break;
            }
        }
        for (int i = 1; i <= 5; i++) printf("%c", ans[i] + 'A');
        puts("");
        fflush(stdout);
        scanf("%s", t);
        if (t[0] == 'N') break;
        // fprintf(stderr, "%d\n", _count);
    }
    return 0;
}
```

### C. Bacterial Tactics
-----

是博弈！

我们发现 R 和 C 都非常小！可以用 SG 函数 + 记忆化搜索。

可以把横向或纵向放细菌，形象看成 横着或竖着 pia 刀。。。

``` c++
---------------
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
---------------
图 1
```
如图 1，我们在中间竖着 pia 了一刀。

``` c++
---------------
|      |      |
|      |      |
|------|      |
|      |      |
|      |      |
---------------
图 2
```
如图 2，我们在左边那块横着 pia 了一刀。我们发现，刀痕有可能会被之前的刀痕半路拦截。所以左边的操作对右边半块毫无影响。

这样好办了：左右半块分别可视为两个子问题。我们用 SG[x1, y1, x2, y2] 表示左上角为 (x1, y1)，右下角为 (x2, y2) 的矩阵的 SG 函数值。枚举竖着 pia 哪一列，横着 pia 哪一行，加上神奇的 mex 函数，就是一个像模像样的博弈解法。别忘判当前操作合法性！

code :
``` c++
#include <bits/stdc++.h>
using namespace std;

int T, n, m, SG[20][20][20][20];
char s[20][20];

int dfs(int x0, int y0, int x2, int y2) {
    if (x0 > x2 || y0 > y2) return 0;
    if (SG[x0][y0][x2][y2] != -1) return SG[x0][y0][x2][y2];  // 记忆化
    bool c[60];
    memset(c, 0, sizeof(c));
    for (int i = x0; i <= x2; i++) {
        // 判合法性
        bool ff = 1;
        for (int j = y0; j <= y2; j++) if (s[i][j] == '#') {
            ff = 0; break;
        } if (!ff) continue;

        c[dfs(x0, y0, i - 1, y2) ^ dfs(i + 1, y0, x2, y2)] = 1;
    }
    for (int i = y0; i <= y2; i++) {
        bool ff = 1;
        for (int j = x0; j <= x2; j++) if (s[j][i] == '#') {
            ff = 0; break;
        } if (!ff) continue;

        c[dfs(x0, y0, x2, i - 1) ^ dfs(x0, i + 1, x2, y2)] = 1;
    }
    for (int i = 0; i < 60; i++) if (!c[i]) {
        SG[x0][y0][x2][y2] = i; return i;
    }
}

int main() {
    scanf("%d", &T);
    for (int cas = 1; cas <= T; cas++) {
        scanf("%d%d", &n, &m);
        memset(SG, -1, sizeof(SG));
        for (int i = 1; i <= n; i++) scanf("%s", s[i] + 1);
        dfs(1, 1, n, m);
        int ans;
        ans = 0;
        for (int i = 1; i <= n; i++) {
            bool ff = 1;
            for (int j = 1; j <= m; j++) if (s[i][j] == '#') {
                ff = 0; break;
            } if (!ff) continue;

            if ((dfs(1, 1, i - 1, m) ^ dfs(i + 1, 1, n, m)) == 0) ans += m;  // 对于对手是必败态，则对于我是必胜态
        }
        for (int i = 1; i <= m; i++) {
            bool ff = 1;
            for (int j = 1; j <= n; j++) if (s[j][i] == '#') {
                ff = 0; break;
            } if (!ff) continue;

            if ((dfs(1, 1, n, i - 1) ^ dfs(1, i + 1, n, m)) == 0) ans += n;
        }
        printf("Case #%d: %d\n", cas, ans);
    }
    return 0;
}
```