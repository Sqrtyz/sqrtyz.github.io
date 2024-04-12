---
title: 【算法竞赛】补题 I
category: XCPC
date: 2023-01-01
---

### ARC163D - Sum of DCC

+ 题意：给定 $n$，$m$，求所有满足以下条件的图 $G$ 的强连通分量数目之和。$n \leq 30, \ m \leq n(n+1)/2$。

  1）$G$ 是 $n$ 个点的竞赛图。

  2）$G$ 中低编号点指向高编号点的边恰有 $m$ 条。

+ 题解：

  考虑一个引理：对于一个确定的竞赛图 $G$，设其 SCC 数目为 $P$；把 $G$ 分为两个点集 $A,B$，使得所有存在于 $A,B$ 之间的边都是 $A$ 指向 $B$ 的方案数为 $Q$。则 $P=Q-1$。

  引理证明不难。考虑所有缩点之后的 SCC 为 $s_1,s_2,...,s_k$。这几个 SCC 作为新点之后，它们依然组成一个**竞赛图**，并且这个图还是一张 DAG。对于一个兼有这两种性质的图，我们只能安排出唯一的一种拓扑序（考虑每次找到入度为 0 的点然后删去，可以证明每轮入度为 0 的点都是唯一的）。假设我们以这种拓扑序重排 $s_1,s_2,...,s_k$，那么唯一的点划分方法就是按照 $A=\{s_1...s_t\}, \ B=\{s_{t+1}...s_{n}\}$ 的形式划分。可以看到，这样的划分方法有 $k+1$ 种，而 $G$ 的 SCC 数目为 $k$，故而引理得证。

  接下来我们考虑 dp。首先我们忘掉 SCC 这个东西——记 $f[n][i][j]$ 为：考虑所有【低编号点指向高编号点的边恰有 $j$ 条】的【$n$ 个点的竞赛图】，按照上述规则【划分为大小为 $i$ 的点集 $A$，大小为 $n-i$ 的点集 $B$】的方案数之和。

  可以发现，最终的答案就是 $\sum f[n][0...n][m] - \binom{n(n-1)/2}{m}$ 的值。

  下面推导递推式。假设目前已计算出 $f[n][i][j]$，考虑它对后面的贡献：

  + 添加第 $n+1$ 个点，且添加到点集 $A$ 中。并且增加的 $n$ 条边中有 $k$ 条是「低指高」的，则对 $f[n+1][i+1][j+k]$ 有 $f[n][i][j] \times \binom{i}{k}$ 的贡献（低指高只存在于 A 内部，想想为什么）。
  + 添加第 $n+1$ 个点，且添加到点集 $B$ 中。并且增加的 $n$ 条边中有 $i+k$ 条是「低指高」的，则对 $f[n+1][i][j+i+k]$ 有 $f[n][i][j] \times \binom{n-i}{k}$ 的贡献（低指高包括 $\{A\} \to n+1$ 共 $i$ 条和  $\{B\} \to n+1$ 共 $k$ 条）。

+ 代码：
  
    ```cpp
    #define MOD 998244353
    #define LL long long

    LL f[32][32][905];

    LL fac[905], facinv[905];
    LL Pow(LL a, LL b) {
        LL ret = 1, base = a;
        while(b) {
            if(b & 1) ret = ret * base % MOD;
            base = base * base % MOD;
            b >>= 1;
        }
        return ret;
    }
    LL C(int a, int b) {
        return fac[a] * facinv[b] % MOD * facinv[a - b] % MOD;
    }
    void Prepare() {
        fac[0] = facinv[0] = 1;
        for(int i = 1; i <= 900; ++i) fac[i] = fac[i - 1] * i % MOD;
        facinv[900] = Pow(fac[900], MOD - 2);
        for(int i = 899; i; --i) facinv[i] = facinv[i + 1] * (i + 1) % MOD;
    }

    int main() {
        int n = read(), m = read();
        Prepare();
        f[1][0][0] = f[1][1][0] = 1;
        for(int ver = 1; ver <= n; ++ver) {
            for(int i = 0; i <= ver; ++i) {
                for(int j = 0; j <= ver * (ver - 1) / 2; ++j) {
                    for(int k = 0; k <= i; ++k) {
                        f[ver + 1][i + 1][j + k] += f[ver][i][j] * C(i, k) % MOD;
                        f[ver + 1][i + 1][j + k] %= MOD;
                    }
                    for(int k = 0; k <= ver - i; ++k) {
                        f[ver + 1][i][j + i + k] += f[ver][i][j] * C(ver - i, k) % MOD;
                        f[ver + 1][i][j + i + k] %= MOD;
                    }
                }
            }
        }
        LL ans = 0;
        for(int i = 0; i <= n; ++i) ans = (ans + f[n][i][m]) % MOD;
        ans -= C(n * (n - 1) / 2, m);
        ans = (ans % MOD + MOD) % MOD;
        cout << ans << endl;
        return 0;
    }
    ```