# 点双连通分量与割点

## 割点

如果删掉某个点及其相连的边，无向图的连通区数量增加了，这个点就叫割点。

1. 推荐先学习tarjan算法求解强连通分量；
2. 割点的求解过程，依然是tarjan算法的微调，依然利用 `dfn` 和 `low` 数组，进行扎口袋；
3. 边的类型依然有树边、回边、弃边；
4. 一条树边，上方点为 `u` ，下方点为 `v` ，如果发现 `low[v] >= dfn[u]` ，那么节点 `u` 就扎起了口袋；
5. 如果 `u` 是连通区的根节点，并且树边连接的儿子数量 `>= 2` ，那么 `u` 是割点；
6. 如果 `u` 不是连通区的根节点，但是 `u` 扎起了口袋，那么 `u` 是割点；
7. 遇到回边时 `low[u] = min(low[u], dfn[v])` ，必须用 `dfn[v]` ，不能用 `low[v]` 。

## 双连通分量（Biconnected Component）

1. 点双连通分量（V-BCC），也可称V-DCC；
2. 无向图中，如果有两个点 `u` 和 `v` ，随便删去一个其他点，它们依然连通，就说 `u` 和 `v` 是点双连通的；
3. 无向图中，一个极大点双连通的子图，叫做一个点双连通分量；推荐先学习tarjan算法求解强连通分量；
4. 点双连通分量的求解过程，依然是tarjan算法的微调，依然利用 `dfn` 和 `low` 数组，进行扎口袋；
5. 边的类型依然有树边、回边、弃边；
6. 来到节点 `u` 时，先设置 `dfn[u]` 和 `low[u]` 的初始值，然后节点 `u` 进栈；
7. 一条树边，上方点为 `u` ，下方点为 `v` ，如果发现 `low[v] >= dfn[u]` ，那么节点 `u` 就扎起了口袋；
8. 一旦扎起口袋，从栈中弹出节点，生成新的点双连通分量，遇到 `u` 停止， `u` 不从栈中弹出；
9. 遇到回边时， `low[u] = min(low[u], dfn[v])` ，必须用 `dfn[v]` ，不能用 `low[v]` ；
10. 无向图中，割点分割出的一块一块的子图，就是一个一个的点双连通分量；
11. 每个割点，在相邻的点双连通分量里都会出现；
12. 每条割边，两个端点会形成一个点双连通分量；
13. 节点数为 `n` ，所有点双连通分量的全部节点可能到达 `2 * n` 的规模。

## [P3388 【模板】割点（割顶）](https://www.luogu.com.cn/problem/P3388)

> - **Question 1**
>   - 给定一张无向图，一共 `n` 个点、 `m` 条边，不保证所有点连通，打印图中割点的数量，然后从小到大打印所有割点的编号。
>   - **Tips**
>     - `1 <= n <= 2 * 10^4`
>     - `1 <= m <= 10^5`

## [P8435 【模板】点双连通分量](https://www.luogu.com.cn/problem/P8435)

> - **Question 2**
>   - 给定一张无向图，一共 `n` 个点、 `m` 条边，建图时忽略自环，打印点双连通分量的个数，打印每个点双连通分量的大小和节点编号，打印顺序随意，每个孤立点分配一个点双连通分量。
>   - **Tips**
>     - `1 <= n <= 5 * 10^5`
>     - `1 <= m <= 2 * 10^6`

## Java

> - **Question 1: 割点模版题**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 20001;
    public static int MAXM = 100001;
    public static int n, m;

    public static int[] head = new int[MAXN];
    public static int[] nxt = new int[MAXM << 1];
    public static int[] to = new int[MAXM << 1];
    public static int cntg;

    public static int[] dfn = new int[MAXN];
    public static int[] low = new int[MAXN];
    public static int cntd;

    public static boolean[] cutVertex = new boolean[MAXN];

    public static int[][] stack = new int[MAXN][5];
    public static int u, root, son, status, e;
    public static int stacksize;

    public static void push(int u, int root, int son, int status, int e) {
        stack[stacksize][0] = u;
        stack[stacksize][1] = root;
        stack[stacksize][2] = son;
        stack[stacksize][3] = status;
        stack[stacksize][4] = e;
        stacksize++;
    }

    public static void pop() {
        stacksize--;
        u = stack[stacksize][0];
        root = stack[stacksize][1];
        son = stack[stacksize][2];
        status = stack[stacksize][3];
        e = stack[stacksize][4];
    }

    public static void addEdge(int u, int v) {
        nxt[++cntg] = head[u];
        to[cntg] = v;
        head[u] = cntg;
    }

    // 递归版，Tarjan算法求解割点
    public static void tarjan1(int u, boolean root) {
        dfn[u] = low[u] = ++cntd;
        int son = 0;
        for (int e = head[u]; e > 0; e = nxt[e]) {
            int v = to[e];
            if (dfn[v] == 0) {
                son++;
                tarjan1(v, false);
                low[u] = Math.min(low[u], low[v]);
                if (low[v] >= dfn[u]) {
                    if (!root || son >= 2) {
                        cutVertex[u] = true;
                    }
                }
            } else {
                low[u] = Math.min(low[u], dfn[v]);
            }
        }
    }

    // 迭代版，Tarjan算法求解割点
    public static void tarjan2(int node, boolean rt) {
        stacksize = 0;
        push(node, rt ? 1 : 0, 0, -1, -1);
        int v;
        while (stacksize > 0) {
            pop();
            if (status == -1) {
                dfn[u] = low[u] = ++cntd;
                e = head[u];
            } else {
                v = to[e];
                if (status == 0) {
                    low[u] = Math.min(low[u], low[v]);
                    if (low[v] >= dfn[u]) {
                        if (root == 0 || son >= 2) {
                            cutVertex[u] = true;
                        }
                    }
                } else {
                    low[u] = Math.min(low[u], dfn[v]);
                }
                e = nxt[e];
            }
            if (e != 0) {
                v = to[e];
                if (dfn[v] == 0) {
                    son++;
                    push(u, root, son, 0, e);
                    push(v, 0, 0, -1, -1);
                } else {
                    push(u, root, son, 1, e);
                }
            }
        }
    }

    public static void main(String[] args) throws Exception {
        FastReader in = new FastReader(System.in);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        n = in.nextInt();
        m = in.nextInt();
        for (int i = 1, u, v; i <= m; i++) {
            u = in.nextInt();
            v = in.nextInt();
            addEdge(u, v);
            addEdge(v, u);
        }
        for (int i = 1; i <= n; i++) {
            if (dfn[i] == 0) {
                // tarjan1(i, true);
                tarjan2(i, true);
            }
        }
        int cntCnt = 0;
        for (int i = 1; i <= n; i++) {
            if (cutVertex[i]) {
                cntCnt++;
            }
        }
        out.println(cntCnt);
        for (int i = 1; i <= n; i++) {
            if (cutVertex[i]) {
                out.print(i + " ");
            }
        }
        out.println();
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

> - **Question 2: 点双连通分量模版题**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 500001;
    public static int MAXM = 2000001;
    public static int n, m;

    public static int[] head = new int[MAXN];
    public static int[] nxt = new int[MAXM << 1];
    public static int[] to = new int[MAXM << 1];
    public static int cntg;

    public static int[] dfn = new int[MAXN];
    public static int[] low = new int[MAXN];
    public static int cntd;

    public static int[] sta = new int[MAXN];
    public static int top;

    public static int[] vbccSiz = new int[MAXN];
    public static int[] vbccArr = new int[MAXN << 1];
    public static int[] vbccl = new int[MAXN];
    public static int[] vbccr = new int[MAXN];
    public static int idx;
    public static int vbccCnt;

    public static int[][] stack = new int[MAXN][3];
    public static int u, status, e;
    public static int stacksize;

    public static void push(int u, int status, int e) {
        stack[stacksize][0] = u;
        stack[stacksize][1] = status;
        stack[stacksize][2] = e;
        stacksize++;
    }

    public static void pop() {
        stacksize--;
        u = stack[stacksize][0];
        status = stack[stacksize][1];
        e = stack[stacksize][2];
    }

    public static void addEdge(int u, int v) {
        nxt[++cntg] = head[u];
        to[cntg] = v;
        head[u] = cntg;
    }

    // 递归版，Tarjan算法求解点双连通分量
    public static void tarjan1(int u) {
        dfn[u] = low[u] = ++cntd;
        sta[++top] = u;
        for (int e = head[u]; e > 0; e = nxt[e]) {
            int v = to[e];
            if (dfn[v] == 0) {
                tarjan1(v);
                low[u] = Math.min(low[u], low[v]);
                if (low[v] >= dfn[u]) {
                    vbccCnt++;
                    vbccSiz[vbccCnt] = 1;
                    vbccArr[++idx] = u;
                    vbccl[vbccCnt] = idx;
                    int pop;
                    do {
                        pop = sta[top--];
                        vbccSiz[vbccCnt]++;
                        vbccArr[++idx] = pop;
                    } while (pop != v);
                    vbccr[vbccCnt] = idx;
                }
            } else {
                low[u] = Math.min(low[u], dfn[v]);
            }
        }
    }

    // 迭代版，Tarjan算法求解点双连通分量
    public static void tarjan2(int node) {
        stacksize = 0;
        push(node, -1, -1);
        int v;
        while (stacksize > 0) {
            pop();
            if (status == -1) {
                dfn[u] = low[u] = ++cntd;
                sta[++top] = u;
                e = head[u];
            } else {
                v = to[e];
                if (status == 0) {
                    low[u] = Math.min(low[u], low[v]);
                    if (low[v] >= dfn[u]) {
                        vbccCnt++;
                        vbccSiz[vbccCnt] = 1;
                        vbccArr[++idx] = u;
                        vbccl[vbccCnt] = idx;
                        int pop;
                        do {
                            pop = sta[top--];
                            vbccSiz[vbccCnt]++;
                            vbccArr[++idx] = pop;
                        } while (pop != v);
                        vbccr[vbccCnt] = idx;
                    }
                } else {
                    low[u] = Math.min(low[u], dfn[v]);
                }
                e = nxt[e];
            }
            if (e != 0) {
                v = to[e];
                if (dfn[v] == 0) {
                    push(u, 0, e);
                    push(v, -1, -1);
                } else {
                    push(u, 1, e);
                }
            }
        }
    }

    public static void main(String[] args) throws Exception {
        FastReader in = new FastReader(System.in);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        n = in.nextInt();
        m = in.nextInt();
        for (int i = 1, u, v; i <= m; i++) {
            u = in.nextInt();
            v = in.nextInt();
            if (u != v) {
                addEdge(u, v);
                addEdge(v, u);
            }
        }
        for (int i = 1; i <= n; i++) {
            if (dfn[i] == 0) {
                if (head[i] == 0) {
                    vbccCnt++;
                    vbccSiz[vbccCnt] = 1;
                    vbccArr[++idx] = i;
                    vbccl[vbccCnt] = vbccr[vbccCnt] = idx;
                } else {
                    // tarjan1(i);
                    tarjan2(i);
                }
            }
        }
        out.println(vbccCnt);
        for (int i = 1; i <= vbccCnt; i++) {
            out.println(vbccSiz[i]);
            for (int j = vbccl[i]; j <= vbccr[i]; j++) {
                out.print(vbccArr[j] + " ");
            }
            out.println();
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
