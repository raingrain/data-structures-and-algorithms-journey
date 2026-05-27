# Number of Simple Paths

## [E. Number of Simple Paths](https://codeforces.com/problemset/problem/1454/E)

> - **Question**
>   - 图中有 `n` 个点、 `n` 条无向边，图是一棵基环树，路径长度为边的数量，要求 `路径长度 >= 1` ，求这样的路径数量是多少，路径 `a, b, c` 和 `c, b, a` ，认为是同一条简单路径，不要重复计算。
>   - **Tips**
>     - `3 <= n <= 2 * 10^5`

## Java

> - **标记环上所有点 + 每个点的分树上统计节点数**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 200001;
    public static int t, n;

    public static int[] head = new int[MAXN];
    public static int[] nxt = new int[MAXN << 1];
    public static int[] to = new int[MAXN << 1];
    public static int cntg;

    public static int[] dfn = new int[MAXN];
    public static int cntd;

    public static int[] from = new int[MAXN];
    public static boolean[] cycle = new boolean[MAXN];

    public static long[] siz = new long[MAXN];

    public static void addEdge(int u, int v) {
        nxt[++cntg] = head[u];
        to[cntg] = v;
        head[u] = cntg;
    }

    public static void dfs(int u) {
        dfn[u] = ++cntd;
        for (int e = head[u]; e > 0; e = nxt[e]) {
            int v = to[e];
            if (dfn[v] == 0) {
                from[v] = u;
                dfs(v);
            } else if (dfn[u] < dfn[v]) {
                cycle[u] = true;
                for (int i = v; i != u; i = from[i]) {
                    cycle[i] = true;
                }
            }
        }
    }

    public static void dpOnTree(int u, int fa) {
        siz[u] = 1;
        for (int e = head[u]; e > 0; e = nxt[e]) {
            int v = to[e];
            if (v != fa && !cycle[v]) {
                dpOnTree(v, u);
                siz[u] += siz[v];
            }
        }
    }

    public static long compute() {
        dfs(1);
        long ans = 0;
        for (int i = 1; i <= n; i++) {
            if (cycle[i]) {
                dpOnTree(i, 0);
                ans += siz[i] * (siz[i] - 1) / 2 + siz[i] * (n - siz[i]);
            }
        }
        return ans;
    }

    public static void prepare() {
        cntg = cntd = 0;
        for (int i = 1; i <= n; i++) {
            head[i] = dfn[i] = 0;
            cycle[i] = false;
        }
    }

    public static void main(String[] args) throws Exception {
        FastReader in = new FastReader(System.in);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        t = in.nextInt();
        for (int c = 1; c <= t; c++) {
            n = in.nextInt();
            prepare();
            for (int i = 1, u, v; i <= n; i++) {
                u = in.nextInt();
                v = in.nextInt();
                addEdge(u, v);
                addEdge(v, u);
            }
            long ans = compute();
            out.println(ans);
        }
        out.flush();
        out.close();
    }

    // 读写工具类
    static class FastReader {

        private final byte[] buffer = new byte[1 << 16];
        private int ptr = 0, len = 0;
        private final InputStream in;

        FastReader(InputStream in) {
            this.in = in;
        }

        private int readByte() throws IOException {
            if (ptr >= len) {
                len = in.read(buffer);
                ptr = 0;
                if (len <= 0)
                    return -1;
            }
            return buffer[ptr++];
        }

        int nextInt() throws IOException {
            int c;
            do {
                c = readByte();
            } while (c <= ' ' && c != -1);
            boolean neg = false;
            if (c == '-') {
                neg = true;
                c = readByte();
            }
            int val = 0;
            while (c > ' ' && c != -1) {
                val = val * 10 + (c - '0');
                c = readByte();
            }
            return neg ? -val : val;
        }
    }

}
```
