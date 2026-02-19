# 割边

1. 无向图在建图时，让边的序号从2开始使用，并且让正反边相邻建立
2. 如果一条边的序号是e，那么反向边的序号是(e ^ 1)，无向边的序号是(e >> 1)
3. 无向图中，如果删除掉某条边之后，连通区的数量增加了，这条边就叫做割边，也叫做桥

## Tarjan算法求解割边

1. 先学习Tarjan算法求解强连通分量；
2. 割边的求解过程，就是tarjan算法的微调，依然利用dfn和low数组，进行扎口袋；
3. 边的类型依然有树边、回边、弃边，注意！到达当前点的那条无向边需要忽略；
4. 如果一条树边，上方点为u，下方点为v，发现 low[v] > dfn[u]，那么该边为割边。

## [U582665 【模板】割边](https://www.luogu.com.cn/problem/U582665)

> - **Question 1**
>   - 给定一张无向图，一共 `n` 个点、 `m` 条边，图中可能存在多个连通区，对每个连通区求割边，先打印割边的总数量，然后从小到大打印所有割边的序号，请保证原图即使有重边和自环，答案依然正确。
>   - **Tips**
>     - `1 <= n <= 5 * 10^5`
>     - `1 <= m <= 2 * 10^6`

## [1192. 查找集群内的关键连接](https://leetcode.cn/problems/critical-connections-in-a-network/)

> - **Question 2**
>   - 力扣数据中心有 `n` 台服务器，分别按从 `0` 到 `n - 1` 的方式进行了编号。它们之间以服务器到服务器的形式相互连接组成了一个内部集群，连接是无向的。用  `connections` 表示集群网络， `connections[i] = [a, b]` 表示服务器 `a` 和 `b` 之间形成连接。任何服务器都可以直接或者间接地通过网络到达任何其他服务器。关键连接是在该集群中的重要连接，假如我们将它移除，便会导致某些服务器无法访问其他服务器。请你以任意顺序返回该集群内的所有关键连接。
>   - **Tips**
>     - `2 <= n <= 10^5`
>     - `n - 1 <= connections.length <= 10^5`
>     - `0 <= ai, bi <= n - 1`
>     - `ai != bi`
>     - 不存在重复的连接

## Java

> - **Question 1**

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

    public static boolean[] cutEdge = new boolean[MAXM];

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
        for (int e = head[u]; e > 0; e = nxt[e]) {
            if ((e ^ 1) == preEdge) { // 从哪条边来的要忽略
                continue;
            }
            int v = to[e];
            if (dfn[v] == 0) { // 树边
                tarjan1(v, e);
                low[u] = Math.min(low[u], low[v]);
                if (low[v] > dfn[u]) {
                    cutEdge[e >> 1] = true;
                }
            } else { // 回边或弃边
                // 回边是dfn[v] < dfn[u]，弃边是dfn[v] > dfn[u]
                // 因为low[u]初始值就是dfn[u]，所以弃边出现时，无法更新low[u]
                low[u] = Math.min(low[u], dfn[v]);
            }
        }
    }

    // 迭代版
    // u表示当前节点，preEdge表示来边
    // e表示u当前处理的边
    //     如果(e ^ 1) == preEdge，跳过当前边，对应递归版中的第一个if
    //     如果e == 0，说明所有边都处理完了
    // status的具体说明如下
    //     如果status == -1，表示u没有遍历过任何儿子
    //     如果status == 0，表示u遍历到儿子v，然后发现dfn[v] == 0
    //         并且执行完了tarjan(v, e)，对应递归版for循环的第二个if
    //     如果status == 1，表示u遍历到儿子v，然后发现dfn[v] != 0
    //         对应递归版for循环中的else分支
    public static void tarjan2(int node, int pree) {
        stacksize = 0;
        push(node, pree, -1, -1);
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
                    if (low[v] > dfn[u]) {
                        cutEdge[e >> 1] = true;
                    }
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
        int ansCnt = 0;
        for (int i = 1; i <= m; i++) {
            if (cutEdge[i]) {
                ansCnt++;
            }
        }
        out.println(ansCnt);
        for (int i = 1; i <= m; i++) {
            if (cutEdge[i]) {
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

> - **Question 2**

```java
class Solution {

    public static int MAXN = 100001;
    public static int MAXM = 100001;
    public static int n, m;
    public static int[] a = new int[MAXM];
    public static int[] b = new int[MAXM];

    public static int[] head = new int[MAXN];
    public static int[] nxt = new int[MAXM << 1];
    public static int[] to = new int[MAXM << 1];
    public static int cntg;

    public static int[] dfn = new int[MAXN];
    public static int[] low = new int[MAXN];
    public static int cntd;

    public static boolean[] cutEdge = new boolean[MAXM];

    public static void prepare() {
        cntg = 1;
        cntd = 0;
        for (int i = 1; i <= n; i++) {
            head[i] = dfn[i] = low[i] = 0;
        }
        for (int i = 1; i <= m; i++) {
            cutEdge[i] = false;
        }
    }

    public static void addEdge(int u, int v) {
        nxt[++cntg] = head[u];
        to[cntg] = v;
        head[u] = cntg;
    }

    public static void tarjan(int u, int preEdge) {
        dfn[u] = low[u] = ++cntd;
        for (int e = head[u]; e > 0; e = nxt[e]) {
            if ((e ^ 1) == preEdge) {
                continue;
            }
            int v = to[e];
            if (dfn[v] == 0) {
                tarjan(v, e);
                low[u] = Math.min(low[u], low[v]);
                if (low[v] > dfn[u]) {
                    cutEdge[e >> 1] = true;
                }
            } else {
                low[u] = Math.min(low[u], dfn[v]);
            }
        }
    }

    public static List<List<Integer>> criticalConnections(int nodeCnt, List<List<Integer>> connections) {
        n = nodeCnt;
        m = connections.size();
        prepare();
        for (int i = 1, j = 0; i <= m; i++, j++) {
            a[i] = connections.get(j).get(0) + 1;
            b[i] = connections.get(j).get(1) + 1;
            addEdge(a[i], b[i]);
            addEdge(b[i], a[i]);
        }
        tarjan(1, 0);
        List<List<Integer>> ans = new ArrayList<>();
        for (int i = 1; i <= m; i++) {
            if (cutEdge[i]) {
                ans.add(Arrays.asList(a[i] - 1, b[i] - 1));
            }
        }
        return ans;
    }

}
```
