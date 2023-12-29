---
title: Codeforces Beta Round 2
date: 2023-12-29 10:17:21
tags: Codeforces
categories: Codeforces
---

# [Codeforces Beta Round 2](https://codeforces.com/contest/2)

## [A. Winner 1500](https://codeforces.com/contest/2/problem/A)



```c
#include <bits/stdc++.h>
using namespace std;
pair<string, int> p[1010];
unordered_map<string, int> sum, tmp;
int main() {
    int n, maxn = -0x3f3f3f;
    cin >> n;
    for (int i = 1; i <= n; i++) {
        cin >> p[i].first >> p[i].second;
        sum[p[i].first] += p[i].second;
    }

    for (int i = 1; i <= n; i++) {
        if (sum[p[i].first] > maxn) maxn = sum[p[i].first];
    }
    
    for (int i = 1; i <= n; i++) {
        tmp[p[i].first] += p[i].second;
        if (tmp[p[i].first] >= maxn && sum[p[i].first] == maxn) {
            cout << p[i].first << endl;
            break;
        }
    }
    return 0;
}
```

## [B. The least round way 2000](https://codeforces.com/contest/2/problem/B)



```c
#include <bits/stdc++.h>
using namespace std;
const int maxn = 1010;
int m[2][maxn][maxn];
int dp[2][maxn][maxn];
int vis[2][maxn][maxn];
int n;

int solve(int mark) {
    vis[mark][1][1] = 0;
    dp[mark][1][1] = m[mark][1][1];
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= n; j++) {
            if (i == 1 && j == 1) continue;
            if (i == 1) {
                dp[mark][i][j] = dp[mark][i][j - 1] + m[mark][i][j];
                vis[mark][i][j] = 1;
            } else if (j == 1) {
                dp[mark][i][j] = dp[mark][i - 1][j] + m[mark][i][j];
                vis[mark][i][j] = 0;
            } else {
                int tt1 = dp[mark][i - 1][j];
                int tt2 = dp[mark][i][j - 1];
                if (tt1 < tt2) {
                    dp[mark][i][j] = tt1 + m[mark][i][j];
                    vis[mark][i][j] = 0;
                } else {
                    dp[mark][i][j] = tt2 + m[mark][i][j];
                    vis[mark][i][j] = 1;
                }
            }
        }
    }
    return dp[mark][n][n];
}

void print(int mark, int x, int y) {
    if (x == 1 && y == 1) return;
    if (vis[mark][x][y] == 0) {
        print(mark, x - 1, y);
        cout << "D";
    } else {
        print(mark, x, y - 1);
        cout << "R";
    }
}


int main() {
    int x, y, flag = 0;
    int tt, t1, t2;
    cin >> n;
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= n; j++) {
            cin >> tt;
            if (tt == 0) {
                flag = 1;
                x = i, y = j;
                continue;
            }
            t1 = t2 = tt;
            while (t1 % 2 == 0) {
                t1 /= 2;
                m[0][i][j]++;
            }
            while (t2 % 5 == 0) {
                t2 /= 5;
                m[1][i][j]++;
            }
        }
    }

    int ans1 = solve(0), ans2 = solve(1);
    int mark = 0, ans = 0;
    mark = ans1 < ans2 ? 0 : 1;
    ans = ans1 < ans2 ? ans1 : ans2;
    
    if (flag && ans > 1) {
        cout << 1 << endl;
        for (int i = 2; i <= x; i++) cout << "D";
        for (int j = 2; j <= n; j++) cout << "R";
        for (int i = x + 1; i <= n; i++) cout << "D";
    } else {
        cout << ans << endl;
        print(mark, n, n);
    }

    return 0;
}
```

## [C. Commentator problem 2600](https://codeforces.com/contest/2/problem/C)

```c
#include<bits/stdc++.h>
using namespace std;

double x[3], y[3], r[3];

double calc(double x1, double y1){
    double ret = 0;
    double t[3];
    for(int i = 0; i < 3; i++)
        t[i] = sqrt((x1 - x[i]) * (x1 - x[i]) + (y1 - y[i]) * (y1 - y[i])) / r[i];
    ret = (t[0] - t[1]) * (t[0] - t[1]) + (t[1] - t[2]) * (t[1] - t[2]) + (t[2] - t[0]) * (t[2] - t[0]);
    return ret;
}

int main(){
    for(int i = 0; i < 3; i++)
        cin >> x[i] >> y[i] >> r[i];

    double tx = 0, ty = 0;

    for(int i = 0; i < 3; i++)
        tx += x[i] / 3, ty += y[i] / 3;

    double s = 1;

    while (s > 1e-6){
        if (calc(tx, ty) > calc(tx + s, ty))
            tx += s;
        else if (calc(tx, ty) > calc(tx - s, ty))
            tx -= s;
        else if (calc(tx, ty) > calc(tx, ty + s))
            ty += s;
        else if (calc(tx, ty) > calc(tx, ty - s))
            ty -= s;
        else
            s *= 0.7;
    }

    if (calc(tx, ty) < 1e-5)
        cout << fixed << setprecision(5) << tx << " " << ty << endl;

    return 0;
}