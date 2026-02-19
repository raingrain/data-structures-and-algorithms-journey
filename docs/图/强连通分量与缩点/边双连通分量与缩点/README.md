# 边双连通分量与缩点

双连通分量（Biconnected component）

## 边双连通分量（E-BCC/E-DCC）

1. 无向图中，如果有两个点 `u` 和 `v` ，随便删去一条边，它们依然连通，就说 `u` 和 `v` 是边双连通的；
2. 无向图中，一个极大边双连通的子图，叫做一个边双连通分量。

## Tarjan算法求解边双连通分量

1. 先学习Tarjan算法求解强连通分量；
2. 求解边双连通分量的过程，依然是Tarjan算法的微调，依然忽略到达当前点的那条无向边；
3. 如果发现 `dfn[u] == low[u]` ，那么 `u` 的上方边，要么没有边，要么是割边；
4. 类似强连通分量的生成方式，可以生成一个边双连通分量；
5. 边双连通分量进行缩点，每个连通区会变成一棵树。

## 点双连通分量（V-BCC/V-DCC）

1. 无向图中，如果有两个点 `u` 和 `v` ，随便删去一个其他点，它们依然连通，就说 `u` 和 `v` 是点双连通的；
2. 无向图中，一个极大点双连通的子图，叫做一个点双连通分量。

## [P8436 【模板】边双连通分量](https://www.luogu.com.cn/problem/P8436)

> - **Question**
>   - 给定一张无向图，一共 `n` 个点、 `m` 条边，图中可能存在多个连通区，对每个连通区求边双连通分量，先打印边双连通分量的总数量，然后对每个边双连通分量，打印节点个数，然后任意顺序打印该边双连通分量的节点，请保证原图即使有重边和自环，答案依然正确。
>   - **Tips**
>     - `1 <= n <= 5 * 10^5`
>     - `1 <= m <= 2 * 10^6`

## Java

> - **边双连通分量模版题**

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

    public static int[] ebccSiz = new int[MAXN];
    public static int[] ebccArr = new int[MAXN];
    public static int[] ebccl = new int[MAXN];
    public static int[] ebccr = new int[MAXN];
    public static int idx;
    public static int ebccCnt;

    public static int[][] stack = new int[MAXN][4];
    public static int u, preEdge, status, e;
    public static int stacksize;

    public static void push(int u, int preEdge, int status, int e) {
        stack[stacksize][0] = u;
        stack[stacksize][1] = preEdge;
        stack[stacksize][2] = status;
        stack[stacksize][3] = e;
        stacksize++;
    }

    public static void pop() {
        stacksize--;
        u = stack[stacksize][0];
        preEdge = stack[stacksize][1];
        status = stack[stacksize][2];
        e = stack[stacksize][3];
    }

    public static void addEdge(int u, int v) {
        nxt[++cntg] = head[u];
        to[cntg] = v;
        head[u] = cntg;
    }

    // 递归版
    public static void tarjan1(int u, int preEdge) {
        dfn[u] = low[u] = ++cntd;
        sta[++top] = u;
        for (int e = head[u]; e > 0; e = nxt[e]) {
            if ((e ^ 1) == preEdge) {
                continue;
            }
            int v = to[e];
            if (dfn[v] == 0) {
                tarjan1(v, e);
                low[u] = Math.min(low[u], low[v]);
            } else {
                low[u] = Math.min(low[u], dfn[v]);
            }
        }
        if (dfn[u] == low[u]) {
            ebccCnt++;
            ebccSiz[ebccCnt] = 0;
            ebccl[ebccCnt] = idx + 1;
            int pop;
            do {
                pop = sta[top--];
                ebccSiz[ebccCnt]++;
                ebccArr[++idx] = pop;
            } while (pop != u);
            ebccr[ebccCnt] = idx;
        }
    }

    // 迭代版
    public static void tarjan2(int node, int pree) {
        stacksize = 0;
        push(node, pree, -1, -1);
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
                } else {
                    low[u] = Math.min(low[u], dfn[v]);
                }
                e = nxt[e];
            }
            if ((e ^ 1) == preEdge) {
                e = nxt[e];
            }
            if (e != 0) {
                v = to[e];
                if (dfn[v] == 0) {
                    push(u, preEdge, 0, e);
                    push(v, e, -1, -1);
                } else {
                    push(u, preEdge, 1, e);
                }
            } else {
                if (dfn[u] == low[u]) {
                    ebccCnt++;
                    ebccSiz[ebccCnt] = 0;
                    ebccl[ebccCnt] = idx + 1;
                    int pop;
                    do {
                        pop = sta[top--];
                        ebccSiz[ebccCnt]++;
                        ebccArr[++idx] = pop;
                    } while (pop != u);
                    ebccr[ebccCnt] = idx;
                }
            }
        }
    }

    public static void main(String[] args) throws Exception {
        FastReader in = new FastReader(System.in);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        cntg = 1;
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
                // tarjan1(i, 0);
                tarjan2(i, 0);
            }
        }
        out.println(ebccCnt);
        for (int i = 1; i <= ebccCnt; i++) {
            out.println(ebccSiz[i]);
            for (int j = ebccl[i]; j <= ebccr[i]; j++) {
                out.print(ebccArr[j] + " ");
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
