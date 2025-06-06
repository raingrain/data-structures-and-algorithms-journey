# Blood Cousins

## [CF208E Blood Cousins](https://codeforces.com/problemset/problem/208/E)

> - **Question**
>   - 一共有 `n` 个节点，编号 `1 ~ n` ，给定每个节点的父亲节点编号，父亲节点为 `0` ，说明当前节点是某棵树的头，注意， `n` 个节点组成的是森林结构，可能有若干棵树，一共有 `m` 条查询，每条查询 `x k` ，含义如下，如果 `x` 往上走 `k` 的距离，没有祖先节点，打印 `0` ，如果 `x` 往上走 `k` 的距离，能找到祖先节点 `a` ，那么从 `a` 往下走 `k` 的距离，除了 `x` 之外，可能还有其他节点，这些节点叫做 `x` 的 `k` 级表亲，打印这个表亲的数量。
>   - **Tips**
>     - `1 <= n、m <= 10^5`

## Java

> - **Question**

```java
// For Most Online Judge systems
// 节点u的k级表亲数量 =  节点u的k级祖先的k级后代数量 - 1

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 100001;
    public static int MAXH = 20;
    public static int n, m;
    public static boolean[] root = new boolean[MAXN];

    // 链式前向星
    public static int[] headg = new int[MAXN];
    public static int[] nextg = new int[MAXN];
    public static int[] tog = new int[MAXN];
    public static int cntg;

    // 问题列表
    public static int[] headq = new int[MAXN];
    public static int[] nextq = new int[MAXN];
    public static int[] ansiq = new int[MAXN];
    public static int[] kq = new int[MAXN];
    public static int cntq;

    // 树链剖分
    public static int[] siz = new int[MAXN];
    public static int[] dep = new int[MAXN];
    public static int[] son = new int[MAXN];
    public static int[][] stjump = new int[MAXN][MAXH];

    // 树上启发式合并
    public static int[] depCnt = new int[MAXN];
    public static int[] ans = new int[MAXN];

    public static void addEdge(int u, int v) {
        nextg[++cntg] = headg[u];
        tog[cntg] = v;
        headg[u] = cntg;
    }

    public static void addQuestion(int u, int i, int k) {
        nextq[++cntq] = headq[u];
        ansiq[cntq] = i;
        kq[cntq] = k;
        headq[u] = cntq;
    }

    public static void dfs1(int u, int fa) {
        siz[u] = 1;
        dep[u] = dep[fa] + 1;
        stjump[u][0] = fa;
        for (int p = 1; p < MAXH; p++) {
            stjump[u][p] = stjump[stjump[u][p - 1]][p - 1];
        }
        for (int e = headg[u]; e > 0; e = nextg[e]) {
            dfs1(tog[e], u);
        }
        for (int e = headg[u], v; e > 0; e = nextg[e]) {
            v = tog[e];
            siz[u] += siz[v];
            if (son[u] == 0 || siz[son[u]] < siz[v]) {
                son[u] = v;
            }
        }
    }

    public static int kAncestor(int u, int k) {
        for (int p = MAXH - 1; p >= 0; p--) {
            if (k >= 1 << p) {
                k -= 1 << p;
                u = stjump[u][p];
            }
        }
        return u;
    }

    public static void effect(int u) {
        depCnt[dep[u]]++;
        for (int e = headg[u]; e > 0; e = nextg[e]) {
            effect(tog[e]);
        }
    }

    public static void cancel(int u) {
        depCnt[dep[u]]--;
        for (int e = headg[u]; e > 0; e = nextg[e]) {
            cancel(tog[e]);
        }
    }

    public static void dfs2(int u, int keep) {
        for (int e = headg[u], v; e > 0; e = nextg[e]) {
            v = tog[e];
            if (v != son[u]) {
                dfs2(v, 0);
            }
        }
        if (son[u] != 0) {
            dfs2(son[u], 1);
        }
        depCnt[dep[u]]++;
        for (int e = headg[u], v; e > 0; e = nextg[e]) {
            v = tog[e];
            if (v != son[u]) {
                effect(v);
            }
        }
        for (int i = headq[u]; i > 0; i = nextq[i]) {
            ans[ansiq[i]] = depCnt[dep[u] + kq[i]];
        }
        if (keep == 0) {
            cancel(u);
        }
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        n = (int) in.nval;
        for (int i = 1, father; i <= n; i++) {
            in.nextToken();
            father = (int) in.nval;
            if (father == 0) {
                root[i] = true;
            } else {
                addEdge(father, i);
            }
        }
        for (int i = 1; i <= n; i++) {
            if (root[i]) {
                dfs1(i, 0);
            }
        }
        in.nextToken();
        m = (int) in.nval;
        for (int i = 1, u, k, kfather; i <= m; i++) {
            in.nextToken();
            u = (int) in.nval;
            in.nextToken();
            k = (int) in.nval;
            kfather = kAncestor(u, k);
            if (kfather != 0) {
                addQuestion(kfather, i, k);
            }
        }
        for (int i = 1; i <= n; i++) {
            if (root[i]) {
                dfs2(i, 0);
            }
        }
        for (int i = 1; i <= m; i++) {
            if (ans[i] == 0) {
                out.print("0 ");
            } else {
                out.print((ans[i] - 1) + " ");
            }
        }
        out.println();
        out.flush();
        out.close();
        br.close();
    }

}
```
