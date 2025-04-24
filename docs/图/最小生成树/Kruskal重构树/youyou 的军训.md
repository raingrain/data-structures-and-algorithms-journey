# youyou 的军训

## [P9638 「yyOI R1」youyou 的军训](https://www.luogu.com.cn/problem/P9638)

> - **Question**
>   - 图里有 `n` 个点， `m` 条无向边，每条边给定不同的边权，图里可能有若干个连通的部分，一共有 `q` 条操作，每条操作都是如下的三种类型中的一种：
>     - 操作 `1 x` ：限制变量 `limit` ，把 `limit` 的值改成 `x` 。
>     - 操作 `2 x` ：点 `x` 不能走过任何边权小于 `limit` 的边，打印此时 `x` 所在的连通区域大小。
>     - 操作 `3 x y` ：第 `x` 条边的边权修改为 `y` ，题目保证修改之后，第 `x` 条边的边权排名不变。
>   - **Tips**
>     - `1 <= n、m、q <= 4 * 10^5`

## Java

> - **Question: Kruskal重构树 + 重构树上每个节点建立叶节点数量的信息**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXK = 800001;
    public static int MAXM = 400001;
    public static int MAXH = 20;
    public static int n, m, q;

    // 每条边的信息，节点u、节点v、边权w、边的编号i
    public static int[][] edge = new int[MAXM][4];
    // 边的编号对应重构树上的点的编号
    public static int[] edgeToTree = new int[MAXM];

    // 并查集
    public static int[] father = new int[MAXK];
    public static int[] stack = new int[MAXK];

    // Kruskal重构树
    public static int[] head = new int[MAXK];
    public static int[] next = new int[MAXK];
    public static int[] to = new int[MAXK];
    public static int cntg = 0;
    public static int[] nodeKey = new int[MAXK];
    public static int cntu;

    // 树上dfs，Kruskal重构树的节点，子树上面有几个叶节点
    public static int[] leafsiz = new int[MAXK];
    // 树上dfs，Kruskal重构树的节点，倍增表
    public static int[][] stjump = new int[MAXK][MAXH];

    // 并查集的find方法，需要改成迭代版不然会爆栈，C++实现不需要
    public static int find(int i) {
        int size = 0;
        while (i != father[i]) {
            stack[size++] = i;
            i = father[i];
        }
        while (size > 0) {
            father[stack[--size]] = i;
        }
        return i;
    }

    public static void addEdge(int u, int v) {
        next[++cntg] = head[u];
        to[cntg] = v;
        head[u] = cntg;
    }

    public static void kruskalRebuild() {
        for (int i = 1; i <= n; i++) {
            father[i] = i;
        }
        Arrays.sort(edge, 1, m + 1, (a, b) -> b[2] - a[2]);
        cntu = n;
        for (int i = 1, fx, fy; i <= m; i++) {
            fx = find(edge[i][0]);
            fy = find(edge[i][1]);
            if (fx != fy) {
                father[fx] = father[fy] = ++cntu;
                father[cntu] = cntu;
                nodeKey[cntu] = edge[i][2];
                addEdge(cntu, fx);
                addEdge(cntu, fy);
                edgeToTree[edge[i][3]] = cntu;
            }
        }
    }

    // dfs1是递归函数，需要改成迭代版，不然会爆栈，C++实现不需要
    public static void dfs1(int u, int fa) {
        stjump[u][0] = fa;
        for (int p = 1; p < MAXH; p++) {
            stjump[u][p] = stjump[stjump[u][p - 1]][p - 1];
        }
        for (int e = head[u]; e > 0; e = next[e]) {
            dfs1(to[e], u);
        }
        if (u <= n) {
            leafsiz[u] = 1;
        } else {
            leafsiz[u] = 0;
        }
        for (int e = head[u]; e > 0; e = next[e]) {
            leafsiz[u] += leafsiz[to[e]];
        }
    }

    public static int[][] ufe = new int[MAXK][3];

    public static int stacksize, u, f, e;

    public static void push(int u, int f, int e) {
        ufe[stacksize][0] = u;
        ufe[stacksize][1] = f;
        ufe[stacksize][2] = e;
        stacksize++;
    }

    public static void pop() {
        --stacksize;
        u = ufe[stacksize][0];
        f = ufe[stacksize][1];
        e = ufe[stacksize][2];
    }

    // dfs2是dfs1的迭代版
    public static void dfs2(int cur, int fa) {
        stacksize = 0;
        push(cur, fa, -1);
        while (stacksize > 0) {
            pop();
            if (e == -1) {
                stjump[u][0] = f;
                for (int p = 1; p < MAXH; p++) {
                    stjump[u][p] = stjump[stjump[u][p - 1]][p - 1];
                }
                e = head[u];
            } else {
                e = next[e];
            }
            if (e != 0) {
                push(u, f, e);
                push(to[e], u, -1);
            } else {
                if (u <= n) {
                    leafsiz[u] = 1;
                } else {
                    leafsiz[u] = 0;
                }
                for (int ei = head[u]; ei > 0; ei = next[ei]) {
                    leafsiz[u] += leafsiz[to[ei]];
                }
            }
        }
    }

    public static int query(int u, int limit) {
        for (int p = MAXH - 1; p >= 0; p--) {
            if (stjump[u][p] > 0 && nodeKey[stjump[u][p]] >= limit) {
                u = stjump[u][p];
            }
        }
        return leafsiz[u];
    }

    public static void main(String[] args) {
        FastIO io = new FastIO(System.in, System.out);
        n = io.nextInt();
        m = io.nextInt();
        q = io.nextInt();
        for (int i = 1; i <= m; i++) {
            edge[i][0] = io.nextInt();
            edge[i][1] = io.nextInt();
            edge[i][2] = io.nextInt();
            edge[i][3] = i;
        }
        kruskalRebuild();
        for (int i = 1; i <= cntu; i++) {
            if (i == father[i]) {
                dfs2(i, 0);
            }
        }
        int op, x, y, limit = 0;
        for (int i = 1; i <= q; i++) {
            op = io.nextInt();
            if (op == 1) {
                limit = io.nextInt();
            } else if (op == 2) {
                x = io.nextInt();
                io.writelnInt(query(x, limit));
            } else {
                x = io.nextInt();
                y = io.nextInt();
                if (edgeToTree[x] != 0) {
                    nodeKey[edgeToTree[x]] = y;
                }
            }
        }
        io.flush();
    }

    // 读写工具类
    static class FastIO {
        private final InputStream is;
        private final OutputStream os;
        private final byte[] inbuf = new byte[1 << 16];
        private int lenbuf = 0;
        private int ptrbuf = 0;
        private final StringBuilder outBuf = new StringBuilder();

        public FastIO(InputStream is, OutputStream os) {
            this.is = is;
            this.os = os;
        }

        private int readByte() {
            if (ptrbuf >= lenbuf) {
                ptrbuf = 0;
                try {
                    lenbuf = is.read(inbuf);
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
                if (lenbuf == -1) {
                    return -1;
                }
            }
            return inbuf[ptrbuf++] & 0xff;
        }

        private int skip() {
            int b;
            while ((b = readByte()) != -1) {
                if (b > ' ') {
                    return b;
                }
            }
            return -1;
        }

        public int nextInt() {
            int b = skip();
            if (b == -1) {
                throw new RuntimeException("No more integers (EOF)");
            }
            boolean negative = false;
            if (b == '-') {
                negative = true;
                b = readByte();
            }
            int val = 0;
            while (b >= '0' && b <= '9') {
                val = val * 10 + (b - '0');
                b = readByte();
            }
            return negative ? -val : val;
        }

        public void write(String s) {
            outBuf.append(s);
        }

        public void writeInt(int x) {
            outBuf.append(x);
        }

        public void writelnInt(int x) {
            outBuf.append(x).append('\n');
        }

        public void flush() {
            try {
                os.write(outBuf.toString().getBytes());
                os.flush();
                outBuf.setLength(0);
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        }
    }

}
```
