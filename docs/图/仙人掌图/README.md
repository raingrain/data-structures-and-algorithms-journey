# 仙人掌图

仙人掌图是无向连通图，英文叫 `cactus graph` ，简称 `cactus` ，每条边要么是割边，要么最多出现在一个简单环里，仙人掌可以理解为允许出现简单环的树，普通的树也是仙人掌图，仙人掌的一个简单环是一个点双连通分量，不是一个边双连通分量，仙人掌的割边直接作为树边，仙人掌可以转成圆方树，如此一来，很多仙人掌问题转化成了树上问题。

## 仙人掌的节点

1. 一个节点，有可能是多个环的环顶节点
2. 一个节点，如果不是环顶节点，最多参与一个环
3. 很多情况下，生成环上节点的信息，会刻意不包含环顶节点，就是这个原因
4. 判断仙人掌 或者 生成环上节点的信息，经常利用这个结论

## 仙人掌遍历和找环的过程

1. 割边出现，两个圆点直连
2. 环出现，生成方点，环顶节点连接方点，方点再去连接环中其他节点
3. 利用边判断回头路，边的编号从 `2` 开始，`e的反向边 = e^1`
4. Tarjan算法的一个改版
5. 环中的每条边依次设置归属，尤其环的末尾边分配给环顶节点

```java
// 仙人掌遍历和找环的过程，java版

public class ShowDetail1 {

    public static int MAXN = 100001;
    public static int MAXM = 200001;

    public static int[] head = new int[MAXN];
    public static int[] nxt = new int[MAXM];
    public static int[] to = new int[MAXM];
    public static int[] weight = new int[MAXM];
    public static int cntg;

    public static int[] dfn = new int[MAXN];
    public static int[] low = new int[MAXN];
    public static int cntd;
    public static int[] sta = new int[MAXN];
    public static int top;

    public static int[] fromWeight = new int[MAXN];

    public static void tarjan(int u, int preEdge) {
        dfn[u] = low[u] = ++cntd;
        sta[++top] = u;
        for (int e = head[u]; e > 0; e = nxt[e]) {
            if ((e ^ 1) == preEdge) continue;
            int v = to[e], w = weight[e];
            if (dfn[v] == 0) {
                tarjan(v, e);
                fromWeight[v] = w; // 设置v的from信息
                if (low[v] < dfn[u]) { // 没有扎起口袋
                    low[u] = Math.min(low[u], low[v]);
                } else if (low[v] > dfn[u]) {
                    // 发现割边，实现有关割边的处理
                    top--;
                } else {
                    // 发现了环，实现有关环的处理
                }
            } else {
                if (dfn[v] < dfn[u]) { // 发现回边
                    fromWeight[v] = w; // 闭合边的信息给入环节点
                    low[u] = Math.min(low[u], dfn[v]);
                }
                // 如果发现弃边，一般什么也不做
            }
        }
    }

}
```

## [P4129 [NEERC 2005 / SHOI2006] 仙人掌](https://www.luogu.com.cn/problem/P4129)

> - **Question 1**
>   - 给定 `n` 个点、 `m` 条路径表示无向图，每条路径的相邻两点之间都有边，如果图不是仙人掌打印 `0` ，如果图是仙人掌，可以在图中删除一些边，如果删除后剩下的图仍然连通，就叫有效的删边方案，允许一条边也不删，打印有效删边的方案数，答案可能很大，需要高精度。
>   - **Tips**
>     - `1 <= n <= 2 * 10^4`
>     - `1 <= 边总数 <= 10^6`

## [P3687 [ZJOI2017] 仙人掌](https://www.luogu.com.cn/problem/P3687)

> - **Question 2**
>   - 给定 `n` 个点、 `m` 条边的无向连通图，输入保证没有自环、没有重边，可以在图中加入一些新的边，如果加入后得到的图是仙人掌，就叫有效的加边方案，允许一条边也不加，但不允许加边后，形成自环或二元环，计算有效的加边方案数，答案对 `998244353` 取余，每次输入可能有多组测试数据。
>   - **Tips**
>     - `1 <= n <= 5 * 10^5`
>     - `1 <= m <= 10^6`

## [P6335 [COCI 2007/2008 #1] STAZA](https://www.luogu.com.cn/problem/P6335)

> - **Question 3**
>   - 给定 `n` 个点、 `m` 条边的无向连通图，输入保证图是仙人掌，路线可以从任何城市开始，但必须在1号点结束，路线可以重复经过同一个点，但不能重复经过同一条边，计算最长路线的边数。
>   - **Tips**
>     - `1 <= n <= 10^4`
>     - `1 <= m <= 2 * 10^4`

## [P4410 [HNOI2009] 无归岛](https://www.luogu.com.cn/problem/P4410)

## [P10779 BZOJ4316 小 C 的独立集](https://www.luogu.com.cn/problem/P10779)

> - **Question 4**
>   - 给定 `n` 个点、 `m` 条边的无向连通图，输入保证图是仙人掌，你可以选择一些点，但是图中任意相邻两点不能同时选择，计算能得到的最大点权累加和，第一个测试链接，给定每个点的点权，第二个测试链接，认为每个点的点权是 `1` 。
>   - **Tips**
>     - `1 <= n <= 10^5`
>     - `1 <= m <= 2 * 10^5`

## [P4244 [SHOI2008] 仙人掌图 II](https://www.luogu.com.cn/problem/P4244)

> - **Question 5**
>   - 给定 `n` 个点、 `m` 条路径表示无向图，每条路径的相邻两点之间都有边，点 `x` 到点 `y` 的距离，是指 `x` 到 `y` 的最短路径中的边数，仙人掌直径，是指任意两点距离的最大值，输入保证图是仙人掌，计算仙人掌直径。
>   - **Tips**
>     - `1 <= n <= 5 * 10^4`
>     - `1 <= 边总数 <= 10^6`

## [P5236 【模板】静态仙人掌](https://www.luogu.com.cn/problem/P5236)

> - **Question 6**
>   - 给定 `n` 个点、 `m` 条边的仙人掌图，每条边有边权，没有自环，没有重边，一共 `q` 条查询，每条查询格式为 `x y` ，查询点 `x` 和点 `y` 之间的最短路距离。
>   - **Tips**
>     - `1 <= n、q <= 10^4`
>     - `1 <= m <= 2 * 10^4`
>     - `1 <= 边权 <= 10^5`

## [#87. mx的仙人掌](https://uoj.ac/problem/87)

> - **Question 7**
>   - 给定 `n` 个点、 `m` 条边的仙人掌图，每条边有边权，没有自环，可能有重边，一共 `q` 条查询，格式 `k a1 a2 .. ak` 含义如下：给定的 `k` 个点中，任意选两个点，可以选择相同点，打印它们之间最短路的最大值是多少。
>   - **Tips**
>     - `1 <= n, 查询中k的总和 <= 3 * 10^5`

## [#158. 【清华集训2015】静态仙人掌](https://uoj.ac/problem/158)

> - **Question 8**
>   - 给定 `n` 个点、 `m` 条边的仙人掌图，没有自环，没有重边， `1` 号节点是仙人掌的根，输入保证每个简单环的边数都为奇数，所以点 `x` 到根的最短路和最长路都是唯一的，以点 `x` 为头的子仙人掌，是指删掉根到 `x` 路径上的边后，包含 `x` 的连通块，每个节点只有黑白两种颜色，初始时所有节点为黑，一共有 `q` 条操作，类型如下：操作 `1 x` ：点 `x` 到根的最短路上所有节点，颜色翻转；操作 `2 x` ：点 `x` 到根的最长路上所有节点，颜色翻转；操作 `3 x` ：查询以点 `x` 为头的子仙人掌中，黑色节点的数量。
>   - **Tips**
>     - `1 <= n、m、q <= 5 * 10^4`

## Java

> - **Question 1: 仙人掌删边方案数**

```java
// For Most Online Judge systems

import java.io.*;
import java.math.BigInteger;
import java.util.*;

public class Main {

    public static int MAXN = 20001;
    public static int MAXM = 1000001;
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

    // 统计每个环的边数
    public static int[] edgeCnt = new int[MAXN];
    public static int cntc;

    // 不作为环顶节点的情况下，参与了几个环
    public static int[] cycleCnt = new int[MAXN];

    // 本题需要高精度乘法
    public static BigInteger ans;

    // 递归改迭代需要的栈
    public static int[] stau = new int[MAXN];
    public static int[] stap = new int[MAXN];
    public static int[] stas = new int[MAXN];
    public static int[] stae = new int[MAXN];
    public static int u, preEdge, status, e;
    public static int stacksiz;

    public static void push(int u, int preEdge, int status, int e) {
        stau[stacksiz] = u;
        stap[stacksiz] = preEdge;
        stas[stacksiz] = status;
        stae[stacksiz] = e;
        stacksiz++;
    }

    public static void pop() {
        stacksiz--;
        u = stau[stacksiz];
        preEdge = stap[stacksiz];
        status = stas[stacksiz];
        e = stae[stacksiz];
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
                if (low[v] < dfn[u]) {
                    low[u] = Math.min(low[u], low[v]);
                    cycleCnt[u]++;
                } else if (low[v] > dfn[u]) {
                    top--;
                } else {
                    cntc++;
                    edgeCnt[cntc] = 1;
                    int pop;
                    do {
                        pop = sta[top--];
                        edgeCnt[cntc]++;
                    } while (pop != v);
                }
            } else if (dfn[v] < dfn[u]) {
                low[u] = Math.min(low[u], dfn[v]);
                cycleCnt[u]++;
            }
        }
    }

    // 迭代版
    public static void tarjan2(int node, int pree) {
        stacksiz = 0;
        push(node, pree, -1, -1);
        int v;
        while (stacksiz > 0) {
            pop();
            if (status == -1) {
                dfn[u] = low[u] = ++cntd;
                sta[++top] = u;
                e = head[u];
            } else {
                v = to[e];
                if (status == 0) {
                    if (low[v] < dfn[u]) {
                        low[u] = Math.min(low[u], low[v]);
                        cycleCnt[u]++;
                    } else if (low[v] > dfn[u]) {
                        top--;
                    } else {
                        cntc++;
                        edgeCnt[cntc] = 1;
                        int pop;
                        do {
                            pop = sta[top--];
                            edgeCnt[cntc]++;
                        } while (pop != v);
                    }
                } else {
                    if (dfn[v] < dfn[u]) {
                        low[u] = Math.min(low[u], dfn[v]);
                        cycleCnt[u]++;
                    }
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
        n = in.nextInt();
        m = in.nextInt();
        cntg = 1;
        for (int i = 1, k, x, y; i <= m; i++) {
            k = in.nextInt();
            x = in.nextInt();
            for (int j = 2; j <= k; j++) {
                y = in.nextInt();
                addEdge(x, y);
                addEdge(y, x);
                x = y;
            }
        }
        // tarjan1(1, 0);
        tarjan2(1, 0);
        boolean check = true;
        for (int i = 1; i <= n; i++) {
            if (dfn[i] == 0 || cycleCnt[i] >= 2) {
                check = false;
                break;
            }
        }
        if (check) {
            ans = BigInteger.ONE;
            for (int i = 1; i <= cntc; i++) {
                ans = ans.multiply(BigInteger.valueOf(edgeCnt[i] + 1));
            }
            out.println(ans);
        } else {
            out.println(0);
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

> - **Question 2: 仙人掌加边方案数**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 500001;
    public static int MAXM = 1000001;
    public static int MOD = 998244353;
    public static int t, n, m;

    public static int[] head = new int[MAXN];
    public static int[] nxt = new int[MAXM << 1];
    public static int[] to = new int[MAXM << 1];
    public static boolean[] cycleEdge = new boolean[MAXM << 1];
    public static int cntg;

    public static int[] dfn = new int[MAXN];
    public static int[] low = new int[MAXN];
    public static int cntd;
    public static int[] sta = new int[MAXN];
    public static int top;

    public static int[] fromEdge = new int[MAXN];
    public static int[] cycleCnt = new int[MAXN];

    public static boolean[] vis = new boolean[MAXN];

    // f[i] : 点周围有i个方向，一旦两个方向配对，不可再用
    // 方向也可不配对，总的配对方法数
    public static long[] f = new long[MAXN];

    // dp[u] : u周围的每个支路配对或者不配对，形成的方案数
    // 两个支路配对是指，各自选出一个端点然后连边
    // 如果u是根，认为上方没有支路
    // 如果u不是根，认为上方有支路，内有一个点
    public static long[] dp = new long[MAXN];

    // 递归改迭代需要的栈
    public static int[] stau = new int[MAXN];
    public static int[] stap = new int[MAXN];
    public static int[] stas = new int[MAXN];
    public static int[] staf = new int[MAXN];
    public static int[] stae = new int[MAXN];
    public static int u, preEdge, status, fa, e;
    public static int stacksiz;

    public static void push(int u, int preEdge, int status, int fa, int e) {
        stau[stacksiz] = u;
        stap[stacksiz] = preEdge;
        stas[stacksiz] = status;
        staf[stacksiz] = fa;
        stae[stacksiz] = e;
        stacksiz++;
    }

    public static void pop() {
        stacksiz--;
        u = stau[stacksiz];
        preEdge = stap[stacksiz];
        status = stas[stacksiz];
        fa = staf[stacksiz];
        e = stae[stacksiz];
    }

    public static void addEdge(int u, int v) {
        nxt[++cntg] = head[u];
        to[cntg] = v;
        cycleEdge[cntg] = false;
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
                fromEdge[v] = e;
                if (low[v] < dfn[u]) {
                    low[u] = Math.min(low[u], low[v]);
                    cycleCnt[u]++;
                } else if (low[v] > dfn[u]) {
                    top--;
                } else {
                    int pop;
                    int edge = fromEdge[u];
                    cycleEdge[edge] = cycleEdge[edge ^ 1] = true;
                    do {
                        pop = sta[top--];
                        edge = fromEdge[pop];
                        cycleEdge[edge] = cycleEdge[edge ^ 1] = true;
                    } while (pop != v);
                }
            } else if (dfn[v] < dfn[u]) {
                fromEdge[v] = e;
                low[u] = Math.min(low[u], dfn[v]);
                cycleCnt[u]++;
            }
        }
    }

    // 迭代版
    public static void tarjan2(int node, int pree) {
        stacksiz = 0;
        push(node, pree, -1, 0, -1);
        int v;
        while (stacksiz > 0) {
            pop();
            if (status == -1) {
                dfn[u] = low[u] = ++cntd;
                sta[++top] = u;
                e = head[u];
            } else {
                v = to[e];
                if (status == 0) {
                    fromEdge[v] = e;
                    if (low[v] < dfn[u]) {
                        low[u] = Math.min(low[u], low[v]);
                        cycleCnt[u]++;
                    } else if (low[v] > dfn[u]) {
                        top--;
                    } else {
                        int pop;
                        int edge = fromEdge[u];
                        cycleEdge[edge] = cycleEdge[edge ^ 1] = true;
                        do {
                            pop = sta[top--];
                            edge = fromEdge[pop];
                            cycleEdge[edge] = cycleEdge[edge ^ 1] = true;
                        } while (pop != v);
                    }
                } else {
                    if (dfn[v] < dfn[u]) {
                        fromEdge[v] = e;
                        low[u] = Math.min(low[u], dfn[v]);
                        cycleCnt[u]++;
                    }
                }
                e = nxt[e];
            }
            if ((e ^ 1) == preEdge) {
                e = nxt[e];
            }
            if (e != 0) {
                v = to[e];
                if (dfn[v] == 0) {
                    push(u, preEdge, 0, 0, e);
                    push(v, e, -1, 0, -1);
                } else {
                    push(u, preEdge, 1, 0, e);
                }
            }
        }
    }

    // 递归版
    public static void dpOnTree1(int u, int fa) {
        vis[u] = true;
        long ans = 1;
        int direction = fa == 0 ? 0 : 1;
        for (int e = head[u]; e > 0; e = nxt[e]) {
            int v = to[e];
            if (v != fa && !cycleEdge[e]) {
                dpOnTree1(v, u);
                ans = ans * dp[v] % MOD;
                direction++;
            }
        }
        dp[u] = ans * f[direction] % MOD;
    }

    // 迭代版
    public static void dpOnTree2(int cur, int father) {
        stacksiz = 0;
        push(cur, 0, 0, father, -1);
        while (stacksiz > 0) {
            pop();
            if (e == -1) {
                vis[u] = true;
                e = head[u];
            } else {
                e = nxt[e];
            }
            if (e != 0) {
                push(u, 0, 0, fa, e);
                int v = to[e];
                if (v != fa && !cycleEdge[e]) {
                    push(v, 0, 0, u, -1);
                }
            } else {
                long ans = 1;
                int direction = fa == 0 ? 0 : 1;
                for (int e = head[u]; e > 0; e = nxt[e]) {
                    int v = to[e];
                    if (v != fa && !cycleEdge[e]) {
                        ans = ans * dp[v] % MOD;
                        direction++;
                    }
                }
                dp[u] = ans * f[direction] % MOD;
            }
        }
    }

    public static void prepare() {
        f[0] = f[1] = 1;
        f[2] = 2;
        for (int i = 3; i <= n; i++) {
            f[i] = (f[i - 1] + f[i - 2] * (i - 1)) % MOD;
        }
        for (int i = 1; i <= n; i++) {
            head[i] = dfn[i] = low[i] = fromEdge[i] = cycleCnt[i] = 0;
            vis[i] = false;
            dp[i] = 0;
        }
        cntg = 1;
        cntd = top = 0;
    }

    public static void main(String[] args) throws Exception {
        FastReader in = new FastReader(System.in);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        t = in.nextInt();
        for (int c = 1; c <= t; c++) {
            n = in.nextInt();
            m = in.nextInt();
            prepare();
            for (int i = 1, u, v; i <= m; i++) {
                u = in.nextInt();
                v = in.nextInt();
                addEdge(u, v);
                addEdge(v, u);
            }
            // tarjan1(1, 0);
            tarjan2(1, 0);
            boolean check = true;
            for (int i = 1; i <= n; i++) {
                if (cycleCnt[i] >= 2) {
                    check = false;
                    break;
                }
            }
            if (check) {
                long ans = 1;
                for (int i = 1; i <= n; i++) {
                    if (!vis[i]) {
                        // dpOnTree1(i, 0);
                        dpOnTree2(i, 0);
                        ans = ans * dp[i] % MOD;
                    }
                }
                out.println(ans);
            } else {
                out.println(0);
            }
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

> - **Question 3: 仙人掌最长路线**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 10001;
    public static int MAXM = 20001;
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

    public static int[] cycle = new int[MAXN];

    // f[u] : 在u向下的结构中（以u为头的子仙人掌），从u出发并最终回到u，能走过的最大边数
    public static int[] f = new int[MAXN];

    // g[u] : 在u向下的结构中（以u为头的子仙人掌），从u出发，终点任意，能走过的最大边数
    public static int[] g = new int[MAXN];

    public static void addEdge(int u, int v) {
        nxt[++cntg] = head[u];
        to[cntg] = v;
        head[u] = cntg;
    }

    public static int dpOnCycle(int u, int v) {
        int siz = 0;
        int pop;
        do {
            pop = sta[top--];
            cycle[++siz] = pop;
        } while (pop != v);
        // best表示，这个环上任选一个点作为最后出去的位置
        // 从u沿两侧之一走到它，能够得到的不回到u的最大边数
        int best = 0;
        int sum = 1;
        for (int i = 1; i <= siz; i++) {
            best = Math.max(best, sum + g[cycle[i]]);
            sum += f[cycle[i]] + 1;
        }
        // u可以下方有多个环，所以新增此时的sum
        f[u] += sum;
        int otherSide = sum;
        for (int i = 1; i <= siz; i++) {
            otherSide -= f[cycle[i]] + 1;
            best = Math.max(best, otherSide + g[cycle[i]]);
        }
        return best - sum;
    }

    public static void tarjan(int u, int preEdge) {
        dfn[u] = low[u] = ++cntd;
        sta[++top] = u;
        // f[u]表示从u出发并最终回到u的最大边数
        // delta为了计算，g[u] = f[u] + delta
        // 表示额外选择一条最后不回到u的出口路线，能带来的最大增量
        int delta = 0;
        for (int e = head[u]; e > 0; e = nxt[e]) {
            if ((e ^ 1) == preEdge) {
                continue;
            }
            int v = to[e];
            if (dfn[v] == 0) {
                tarjan(v, e);
                if (low[v] < dfn[u]) {
                    low[u] = Math.min(low[u], low[v]);
                } else if (low[v] > dfn[u]) {
                    top--;
                    delta = Math.max(delta, g[v] + 1);
                } else {
                    delta = Math.max(delta, dpOnCycle(u, v));
                }
            } else if (dfn[v] < dfn[u]) {
                low[u] = Math.min(low[u], dfn[v]);
            }
        }
        g[u] = f[u] + delta;
    }

    public static void main(String[] args) throws Exception {
        FastReader in = new FastReader(System.in);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        n = in.nextInt();
        m = in.nextInt();
        cntg = 1;
        for (int i = 1, u, v; i <= m; i++) {
            u = in.nextInt();
            v = in.nextInt();
            addEdge(u, v);
            addEdge(v, u);
        }
        tarjan(1, 0);
        out.println(g[1]);
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

> - **Question 4: 仙人掌最大独立集**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 100001;
    public static int MAXM = 200001;
    public static int INF = 1000000000;
    public static int n, m;
    public static int[] arr = new int[MAXN];

    public static int[] head = new int[MAXN];
    public static int[] nxt = new int[MAXM << 1];
    public static int[] to = new int[MAXM << 1];
    public static int cntg;

    public static int[] dfn = new int[MAXN];
    public static int[] low = new int[MAXN];
    public static int cntd;
    public static int[] sta = new int[MAXN];
    public static int top;

    // 收集环上的节点，不包含环顶
    public static int[] cycle = new int[MAXN];

    // dp[u][0] : 以u为头的子仙人掌中，不选u的情况下，获得的最大收益
    // dp[u][1] : 以u为头的子仙人掌中，选u的情况下，获得的最大收益
    public static int[][] dp = new int[MAXN][2];

    // 递归改迭代需要的栈
    public static int[] stau = new int[MAXN];
    public static int[] stap = new int[MAXN];
    public static int[] stas = new int[MAXN];
    public static int[] stae = new int[MAXN];
    public static int u, preEdge, status, e;
    public static int stacksiz;

    public static void push(int u, int preEdge, int status, int e) {
        stau[stacksiz] = u;
        stap[stacksiz] = preEdge;
        stas[stacksiz] = status;
        stae[stacksiz] = e;
        stacksiz++;
    }

    public static void pop() {
        stacksiz--;
        u = stau[stacksiz];
        preEdge = stap[stacksiz];
        status = stas[stacksiz];
        e = stae[stacksiz];
    }

    public static void addEdge(int u, int v) {
        nxt[++cntg] = head[u];
        to[cntg] = v;
        head[u] = cntg;
    }

    public static void dpOnCycle(int u, int v) {
        int siz = 0;
        int pop;
        do {
            pop = sta[top--];
            cycle[++siz] = pop;
        } while (pop != v);
        // 不选u
        int pre0 = dp[cycle[1]][0];
        int pre1 = dp[cycle[1]][1];
        int cur0, cur1;
        for (int i = 2; i <= siz; i++) {
            int x = cycle[i];
            cur0 = Math.max(pre0, pre1) + dp[x][0];
            cur1 = pre0 + dp[x][1];
            pre0 = cur0;
            pre1 = cur1;
        }
        dp[u][0] += Math.max(pre0, pre1);
        // 选u
        pre0 = dp[cycle[1]][0];
        pre1 = -INF;
        for (int i = 2; i <= siz; i++) {
            int x = cycle[i];
            cur0 = Math.max(pre0, pre1) + dp[x][0];
            cur1 = pre0 + dp[x][1];
            pre0 = cur0;
            pre1 = cur1;
        }
        dp[u][1] += pre0;
    }

    // 递归版
    public static void tarjan1(int u, int preEdge) {
        dfn[u] = low[u] = ++cntd;
        sta[++top] = u;
        dp[u][0] = 0;
        dp[u][1] = arr[u];
        for (int e = head[u]; e > 0; e = nxt[e]) {
            if ((e ^ 1) == preEdge) {
                continue;
            }
            int v = to[e];
            if (dfn[v] == 0) {
                tarjan1(v, e);
                if (low[v] < dfn[u]) {
                    low[u] = Math.min(low[u], low[v]);
                } else if (low[v] > dfn[u]) {
                    dp[u][0] += Math.max(dp[v][0], dp[v][1]);
                    dp[u][1] += dp[v][0];
                    top--;
                } else {
                    dpOnCycle(u, v);
                }
            } else if (dfn[v] < dfn[u]) {
                low[u] = Math.min(low[u], dfn[v]);
            }
        }
    }

    // 迭代版
    public static void tarjan2(int node, int pree) {
        stacksiz = 0;
        push(node, pree, -1, -1);
        int v;
        while (stacksiz > 0) {
            pop();
            if (status == -1) {
                dfn[u] = low[u] = ++cntd;
                sta[++top] = u;
                dp[u][0] = 0;
                dp[u][1] = arr[u];
                e = head[u];
            } else {
                v = to[e];
                if (status == 0) {
                    if (low[v] < dfn[u]) {
                        low[u] = Math.min(low[u], low[v]);
                    } else if (low[v] > dfn[u]) {
                        dp[u][0] += Math.max(dp[v][0], dp[v][1]);
                        dp[u][1] += dp[v][0];
                        top--;
                    } else {
                        dpOnCycle(u, v);
                    }
                } else {
                    if (dfn[v] < dfn[u]) {
                        low[u] = Math.min(low[u], dfn[v]);
                    }
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
        n = in.nextInt();
        m = in.nextInt();
        cntg = 1;
        for (int i = 1, u, v; i <= m; i++) {
            u = in.nextInt();
            v = in.nextInt();
            addEdge(u, v);
            addEdge(v, u);
        }

        // 洛谷P4410，使用如下的点权设置
        for (int i = 1; i <= n; i++) {
            arr[i] = in.nextInt();
        }

        // 洛谷P10779，使用如下的点权设置
//  for (int i = 1; i <= n; i++) {
//   arr[i] = 1;
//  }

        tarjan2(1, 0);
        out.println(Math.max(dp[1][0], dp[1][1]));
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

> - **Question 5: 仙人掌直径**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 50001;
    public static int MAXM = 1000001;
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

    // dist[u] : 以u为头的子仙人掌中，u到任意点的最短路距离的最大值
    public static int[] dist = new int[MAXN];
    // 收集环中每个点的dist，然后复制一份
    public static int[] arr = new int[MAXN << 1];
    // 单调队列
    public static int[] que = new int[MAXN << 1];
    // 答案
    public static int diameter;

    // 递归改迭代需要的栈
    public static int[] stau = new int[MAXN];
    public static int[] stap = new int[MAXN];
    public static int[] stas = new int[MAXN];
    public static int[] stae = new int[MAXN];
    public static int u, preEdge, status, e;
    public static int stacksiz;

    public static void push(int u, int preEdge, int status, int e) {
        stau[stacksiz] = u;
        stap[stacksiz] = preEdge;
        stas[stacksiz] = status;
        stae[stacksiz] = e;
        stacksiz++;
    }

    public static void pop() {
        stacksiz--;
        u = stau[stacksiz];
        preEdge = stap[stacksiz];
        status = stas[stacksiz];
        e = stae[stacksiz];
    }

    public static void addEdge(int u, int v) {
        nxt[++cntg] = head[u];
        to[cntg] = v;
        head[u] = cntg;
    }

    public static void dpOnCycle(int u, int v) {
        int siz = 0;
        arr[++siz] = dist[u];
        int pop;
        do {
            pop = sta[top--];
            arr[++siz] = dist[pop];
        } while (pop != v);
        for (int i = 1; i <= siz; i++) {
            arr[i + siz] = arr[i];
        }
        int l = 1, r = 0;
        que[++r] = 1;
        for (int i = 2; i <= siz << 1; i++) {
            while (l <= r && (i - que[l]) * 2 > siz) {
                l++;
            }
            diameter = Math.max(diameter, arr[i] + i + arr[que[l]] - que[l]);
            while (l <= r && arr[que[r]] - que[r] <= arr[i] - i) {
                r--;
            }
            que[++r] = i;
        }
        for (int i = 2; i <= siz; i++) {
            dist[u] = Math.max(dist[u], arr[i] + Math.min(i - 1, siz - (i - 1)));
        }
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
                if (low[v] < dfn[u]) {
                    low[u] = Math.min(low[u], low[v]);
                } else if (low[v] > dfn[u]) {
                    top--;
                    diameter = Math.max(diameter, dist[u] + dist[v] + 1);
                    dist[u] = Math.max(dist[u], dist[v] + 1);
                } else {
                    dpOnCycle(u, v);
                }
            } else if (dfn[v] < dfn[u]) {
                low[u] = Math.min(low[u], dfn[v]);
            }
        }
    }

    // 迭代版
    public static void tarjan2(int node, int pree) {
        stacksiz = 0;
        push(node, pree, -1, -1);
        int v;
        while (stacksiz > 0) {
            pop();
            if (status == -1) {
                dfn[u] = low[u] = ++cntd;
                sta[++top] = u;
                e = head[u];
            } else {
                v = to[e];
                if (status == 0) {
                    if (low[v] < dfn[u]) {
                        low[u] = Math.min(low[u], low[v]);
                    } else if (low[v] > dfn[u]) {
                        top--;
                        diameter = Math.max(diameter, dist[u] + dist[v] + 1);
                        dist[u] = Math.max(dist[u], dist[v] + 1);
                    } else {
                        dpOnCycle(u, v);
                    }
                } else {
                    if (dfn[v] < dfn[u]) {
                        low[u] = Math.min(low[u], dfn[v]);
                    }
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
        n = in.nextInt();
        m = in.nextInt();
        cntg = 1;
        for (int i = 1, k, x, y; i <= m; i++) {
            k = in.nextInt();
            x = in.nextInt();
            for (int j = 2; j <= k; j++) {
                y = in.nextInt();
                addEdge(x, y);
                addEdge(y, x);
                x = y;
            }
        }
        // tarjan1(1, 0);
        tarjan2(1, 0);
        out.println(diameter);
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

> - **Question 6: x点向上找到进入某环的入口节点**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 50001;
    public static int n, m, q, cntn;

    // 原图
    public static int[] head1 = new int[MAXN];
    public static int[] next1 = new int[MAXN];
    public static int[] to1 = new int[MAXN];
    public static int[] weight1 = new int[MAXN];
    public static int cnt1;

    // 圆方树，单向边
    public static int[] head2 = new int[MAXN];
    public static int[] next2 = new int[MAXN];
    public static int[] to2 = new int[MAXN];
    public static int[] weight2 = new int[MAXN];
    public static int cnt2;

    // tarjan算法求圆方树
    public static int[] dfn = new int[MAXN];
    public static int[] low = new int[MAXN];
    public static int cntd;
    public static int[] sta = new int[MAXN];
    public static int stasiz;

    // 环的信息
    public static int[] fromWeight = new int[MAXN];
    public static int[] cycleLen = new int[MAXN];
    public static int[] cycleSum = new int[MAXN];

    // 树链剖分
    // len[u] : 圆方树上从头节点到u的长度
    public static int[] fa = new int[MAXN];
    public static int[] dep = new int[MAXN];
    public static int[] siz = new int[MAXN];
    public static int[] len = new int[MAXN];
    public static int[] son = new int[MAXN];
    public static int[] top = new int[MAXN];

    public static void addEdge1(int u, int v, int w) {
        next1[++cnt1] = head1[u];
        to1[cnt1] = v;
        weight1[cnt1] = w;
        head1[u] = cnt1;
    }

    public static void addEdge2(int u, int v, int w) {
        next2[++cnt2] = head2[u];
        to2[cnt2] = v;
        weight2[cnt2] = w;
        head2[u] = cnt2;
    }

    public static void cycleLink(int u, int v) {
        cntn++;
        cycleSum[cntn] = fromWeight[u];
        addEdge2(u, cntn, 0);
        int tmp = stasiz;
        int pop;
        do {
            pop = sta[tmp--];
            cycleLen[pop] = cycleSum[cntn];
            cycleSum[cntn] += fromWeight[pop];
        } while (pop != v);
        do {
            pop = sta[stasiz--];
            addEdge2(cntn, pop, Math.min(cycleLen[pop], cycleSum[cntn] - cycleLen[pop]));
        } while (pop != v);
    }

    public static void tarjan(int u, int preEdge) {
        dfn[u] = low[u] = ++cntd;
        sta[++stasiz] = u;
        for (int e = head1[u]; e > 0; e = next1[e]) {
            if ((e ^ 1) == preEdge) {
                continue;
            }
            int v = to1[e];
            int w = weight1[e];
            if (dfn[v] == 0) {
                tarjan(v, e);
                fromWeight[v] = w;
                if (low[v] < dfn[u]) {
                    low[u] = Math.min(low[u], low[v]);
                } else if (low[v] > dfn[u]) {
                    stasiz--;
                    addEdge2(u, v, w);
                } else {
                    cycleLink(u, v);
                }
            } else if (dfn[v] < dfn[u]) {
                fromWeight[v] = w;
                low[u] = Math.min(low[u], dfn[v]);
            }
        }
    }

    public static void dfs1(int u, int f, int l) {
        fa[u] = f;
        dep[u] = dep[f] + 1;
        siz[u] = 1;
        len[u] = l;
        for (int e = head2[u], v; e > 0; e = next2[e]) {
            v = to2[e];
            // 因为是单向边，所以不用判断 v != f，但这里保留模版写法
            if (v != f) {
                dfs1(v, u, len[u] + weight2[e]);
                siz[u] += siz[v];
                if (son[u] == 0 || siz[son[u]] < siz[v]) {
                    son[u] = v;
                }
            }
        }
    }

    public static void dfs2(int u, int t) {
        top[u] = t;
        if (son[u] != 0) {
            dfs2(son[u], t);
        }
        for (int e = head2[u]; e > 0; e = next2[e]) {
            int v = to2[e];
            // 因为是单向边，所以不用判断 v != fa[u]，但这里保留模版写法
            if (v != fa[u] && v != son[u]) {
                dfs2(v, v);
            }
        }
    }

    public static int lca(int a, int b) {
        while (top[a] != top[b]) {
            if (dep[top[a]] <= dep[top[b]]) {
                b = fa[top[b]];
            } else {
                a = fa[top[a]];
            }
        }
        return dep[a] <= dep[b] ? a : b;
    }

    public static int find(int x, int square) {
        int pre = 0;
        while (top[x] != top[square]) {
            pre = top[x];
            x = fa[pre];
        }
        return x == square ? pre : son[square];
    }

    public static int query(int x, int y) {
        int ans = 0;
        int xylca = lca(x, y);
        if (xylca <= n) {
            ans = len[x] + len[y] - (len[xylca] << 1);
        } else {
            int fx = find(x, xylca);
            int fy = find(y, xylca);
            ans = len[x] + len[y] - len[fx] - len[fy];
            int small = Math.min(cycleLen[fx], cycleLen[fy]);
            int big = Math.max(cycleLen[fx], cycleLen[fy]);
            ans += Math.min(big - small, cycleSum[xylca] - (big - small));
        }
        return ans;
    }

    public static void main(String[] args) throws Exception {
        FastReader in = new FastReader(System.in);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        n = in.nextInt();
        m = in.nextInt();
        q = in.nextInt();
        cntn = n;
        cnt1 = 1;
        for (int i = 1, u, v, w; i <= m; i++) {
            u = in.nextInt();
            v = in.nextInt();
            w = in.nextInt();
            addEdge1(u, v, w);
            addEdge1(v, u, w);
        }
        for (int i = 1; i <= n; i++) {
            if (dfn[i] == 0) {
                tarjan(i, 0);
            }
        }
        dfs1(1, 0, 0);
        dfs2(1, 1);
        for (int i = 1, x, y; i <= q; i++) {
            x = in.nextInt();
            y = in.nextInt();
            out.println(query(x, y));
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

> - **Question 7: 仙人掌 + 树链剖分 + 圆方树 + 虚树 + 直径**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 600001;
    public static int n, m, q, k, cntn;

    // 原图
    public static int[] head1 = new int[MAXN];
    public static int[] next1 = new int[MAXN << 1];
    public static int[] to1 = new int[MAXN << 1];
    public static int[] weight1 = new int[MAXN << 1];
    public static int cnt1;

    // 圆方树
    public static int[] head2 = new int[MAXN];
    public static int[] next2 = new int[MAXN];
    public static int[] to2 = new int[MAXN];
    public static long[] weight2 = new long[MAXN];
    public static int cnt2;

    // 虚树
    public static int[] head3 = new int[MAXN];
    public static int[] next3 = new int[MAXN];
    public static int[] to3 = new int[MAXN];
    public static int cnt3;

    // dfn数组，tarjan和树剖都要使用
    public static int[] dfn = new int[MAXN];
    public static int[] low = new int[MAXN];
    public static int cntd;
    public static int[] sta = new int[MAXN];
    public static int stasiz;

    // 环的信息
    public static long[] fromWeight = new long[MAXN];
    public static long[] cycleLen = new long[MAXN];
    public static long[] cycleSum = new long[MAXN];

    // 树链剖分
    public static int[] fa = new int[MAXN];
    public static int[] dep = new int[MAXN];
    public static int[] siz = new int[MAXN];
    public static long[] len = new long[MAXN];
    public static int[] son = new int[MAXN];
    public static int[] top = new int[MAXN];

    // 批量节点
    public static int[] arr = new int[MAXN];
    public static int[] tmp = new int[MAXN];

    // dist[u] : 以u为头的树，u到任意点的最短路距离的最大值
    public static long[] dist = new long[MAXN];
    public static long diameter;

    // 单调队列
    public static int[] idx = new int[MAXN];
    public static long[] pre = new long[MAXN];
    public static long[] val = new long[MAXN];
    public static int[] que = new int[MAXN];

    public static void addEdge1(int u, int v, int w) {
        next1[++cnt1] = head1[u];
        to1[cnt1] = v;
        weight1[cnt1] = w;
        head1[u] = cnt1;
    }

    public static void addEdge2(int u, int v, long w) {
        next2[++cnt2] = head2[u];
        to2[cnt2] = v;
        weight2[cnt2] = w;
        head2[u] = cnt2;
    }

    public static void addEdge3(int u, int v) {
        next3[++cnt3] = head3[u];
        to3[cnt3] = v;
        head3[u] = cnt3;
    }

    public static void sortByDfn(int[] nums, int l, int r) {
        if (l >= r) return;
        int i = l, j = r;
        int pivot = nums[(l + r) >> 1];
        while (i <= j) {
            while (dfn[nums[i]] < dfn[pivot]) i++;
            while (dfn[nums[j]] > dfn[pivot]) j--;
            if (i <= j) {
                int tmp = nums[i]; nums[i] = nums[j]; nums[j] = tmp;
                i++; j--;
            }
        }
        sortByDfn(nums, l, j);
        sortByDfn(nums, i, r);
    }

    public static void sortByLen(int[] nums, int l, int r) {
        if (l >= r) return;
        int i = l, j = r;
        int pivot = nums[(l + r) >> 1];
        while (i <= j) {
            while (cycleLen[nums[i]] < cycleLen[pivot]) i++;
            while (cycleLen[nums[j]] > cycleLen[pivot]) j--;
            if (i <= j) {
                int tmp = nums[i]; nums[i] = nums[j]; nums[j] = tmp;
                i++; j--;
            }
        }
        sortByLen(nums, l, j);
        sortByLen(nums, i, r);
    }

    public static void cycleLink(int u, int v) {
        cntn++;
        cycleSum[cntn] = fromWeight[u];
        addEdge2(u, cntn, 0);
        int tmp = stasiz;
        int pop;
        do {
            pop = sta[tmp--];
            cycleLen[pop] = cycleSum[cntn];
            cycleSum[cntn] += fromWeight[pop];
        } while (pop != v);
        do {
            pop = sta[stasiz--];
            addEdge2(cntn, pop, Math.min(cycleLen[pop], cycleSum[cntn] - cycleLen[pop]));
        } while (pop != v);
    }

    public static void tarjan(int u, int preEdge) {
        dfn[u] = low[u] = ++cntd;
        sta[++stasiz] = u;
        for (int e = head1[u]; e > 0; e = next1[e]) {
            if ((e ^ 1) == preEdge) {
                continue;
            }
            int v = to1[e];
            long w = weight1[e];
            if (dfn[v] == 0) {
                tarjan(v, e);
                fromWeight[v] = w;
                if (low[v] < dfn[u]) {
                    low[u] = Math.min(low[u], low[v]);
                } else if (low[v] > dfn[u]) {
                    stasiz--;
                    addEdge2(u, v, w);
                } else {
                    cycleLink(u, v);
                }
            } else if (dfn[v] < dfn[u]) {
                fromWeight[v] = w;
                low[u] = Math.min(low[u], dfn[v]);
            }
        }
    }

    public static void dfs1(int u, int f, long l) {
        fa[u] = f;
        dep[u] = dep[f] + 1;
        siz[u] = 1;
        len[u] = l;
        for (int e = head2[u], v; e > 0; e = next2[e]) {
            v = to2[e];
            if (v != f) {
                dfs1(v, u, len[u] + weight2[e]);
                siz[u] += siz[v];
                if (son[u] == 0 || siz[son[u]] < siz[v]) {
                    son[u] = v;
                }
            }
        }
    }

    public static void dfs2(int u, int t) {
        top[u] = t;
        dfn[u] = ++cntd;
        if (son[u] != 0) {
            dfs2(son[u], t);
        }
        for (int e = head2[u]; e > 0; e = next2[e]) {
            int v = to2[e];
            if (v != fa[u] && v != son[u]) {
                dfs2(v, v);
            }
        }
    }

    public static int lca(int a, int b) {
        while (top[a] != top[b]) {
            if (dep[top[a]] <= dep[top[b]]) {
                b = fa[top[b]];
            } else {
                a = fa[top[a]];
            }
        }
        return dep[a] <= dep[b] ? a : b;
    }

    public static int find(int x, int square) {
        int pre = 0;
        while (top[x] != top[square]) {
            pre = top[x];
            x = fa[pre];
        }
        return x == square ? pre : son[square];
    }

    // 虚树的建立方式，二次排序 + 相邻LCA连边
    public static int buildVirtualTree() {
        sortByDfn(arr, 1, k);
        int len = 0;
        for (int i = 1; i < k; i++) {
            tmp[++len] = arr[i];
            tmp[++len] = lca(arr[i], arr[i + 1]);
        }
        tmp[++len] = arr[k];
        sortByDfn(tmp, 1, len);
        int unique = 1;
        for (int i = 2; i <= len; i++) {
            if (tmp[unique] != tmp[i]) {
                tmp[++unique] = tmp[i];
            }
        }
        cnt3 = 0;
        for (int i = 1; i <= unique; i++) {
            head3[tmp[i]] = 0;
        }
        for (int i = 1; i < unique; i++) {
            addEdge3(lca(tmp[i], tmp[i + 1]), tmp[i + 1]);
        }
        return tmp[1];
    }

    public static void computeOnCycle(int u, int siz) {
        sortByLen(idx, 1, siz);
        for (int i = 1; i <= siz; i++) {
            pre[i] = cycleLen[idx[i]];
            pre[i + siz] = pre[i] + cycleSum[u];
            val[i] = dist[idx[i]];
            val[i + siz] = val[i];
        }
        int l = 1;
        int r = 0;
        for (int i = 1; i <= siz * 2; i++) {
            while (l <= r && (pre[i] - pre[que[l]]) * 2 > cycleSum[u]) {
                l++;
            }
            if (l <= r) {
                diameter = Math.max(diameter, val[que[l]] - pre[que[l]] + val[i] + pre[i]);
            }
            while (l <= r && val[que[r]] - pre[que[r]] <= val[i] - pre[i]) {
                r--;
            }
            que[++r] = i;
        }
    }

    public static void dpOnTree(int u) {
        dist[u] = 0;
        if (u <= n) {
            for (int e = head3[u]; e > 0; e = next3[e]) {
                int v = to3[e];
                dpOnTree(v);
                diameter = Math.max(diameter, dist[u] + dist[v] + len[v] - len[u]);
                dist[u] = Math.max(dist[u], dist[v] + len[v] - len[u]);
            }
        } else {
            for (int e = head3[u]; e > 0; e = next3[e]) {
                int v = to3[e];
                dpOnTree(v);
            }
            // 注意，下面的for循环，不能和上面的for循环合并处理
            // 因为idx是全局数组，此时u是方点，会收集环上的相关节点
            // 如果边递归边收集idx，那么当前已经写入的idx[1..siz]
            // 可能会被孩子的递归过程覆盖掉，导致idx收集脏数据
            // 所以先执行所有孩子的递归，再收集环上节点
            int siz = 0;
            for (int e = head3[u]; e > 0; e = next3[e]) {
                int v = to3[e];
                int f = find(v, u);
                dist[f] = dist[v] + len[v] - len[f];
                idx[++siz] = f;
                dist[u] = Math.max(dist[u], dist[v] + len[v] - len[u]);
            }
            computeOnCycle(u, siz);
        }
    }

    public static void main(String[] args) throws Exception {
        FastReader in = new FastReader(System.in);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        n = in.nextInt();
        m = in.nextInt();
        cntn = n;
        cnt1 = 1;
        for (int i = 1, u, v, w; i <= m; i++) {
            u = in.nextInt();
            v = in.nextInt();
            w = in.nextInt();
            addEdge1(u, v, w);
            addEdge1(v, u, w);
        }
        tarjan(1, 0);
        cntd = 0;
        dfs1(1, 0, 0);
        dfs2(1, 1);
        q = in.nextInt();
        for (int i = 1; i <= q; i++) {
            k = in.nextInt();
            for (int j = 1; j <= k; j++) {
                arr[j] = in.nextInt();
            }
            diameter = 0;
            int tree = buildVirtualTree();
            dpOnTree(tree);
            out.println(diameter);
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

> - **Question 8: 仙人掌 + 圆方树 + 重链剖分重新设计dfn序号分配方式 + 线段树 + 很多情况讨论**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 100001;
    public static int n, m, q, cntn;

    // 原图
    public static int[] head1 = new int[MAXN];
    public static int[] next1 = new int[MAXN << 1];
    public static int[] to1 = new int[MAXN << 1];
    public static int cnt1;

    // 圆方树
    public static int[] head2 = new int[MAXN];
    public static int[] next2 = new int[MAXN];
    public static int[] to2 = new int[MAXN];
    public static int cnt2;

    // tarjan算法
    public static int[] dfn = new int[MAXN];
    public static int[] low = new int[MAXN];
    public static int cntd;
    public static int[] sta = new int[MAXN];
    public static int stasiz;

    // 树链剖分
    public static int[] fa = new int[MAXN];
    public static int[] dep = new int[MAXN];
    public static int[] siz = new int[MAXN];
    public static int[] son = new int[MAXN];
    public static int[] top = new int[MAXN];

    // 仙人掌中，一个节点可能是多个环的环顶，但不做环顶的话，最多参与一个环
    // belongCycle[x]，表示节点x归属的环，x不是环顶节点
    public static int[] belongCycle = new int[MAXN];
    // 环顶节点，只对方点有意义
    public static int[] cycleRoot = new int[MAXN];

    // 圆点在环中的位置
    public static int[] pos = new int[MAXN];
    // 环的边数
    public static int[] cycleSum = new int[MAXN];

    // 除了环顶和重儿子之外，环中圆点的dfn编号范围，只对方点有意义
    public static int[] cyclel = new int[MAXN];
    public static int[] cycler = new int[MAXN];

    // 子树上，所有后代节点的dfn编号范围，只对圆点有意义
    public static int[] treel = new int[MAXN];
    public static int[] treer = new int[MAXN];

    // dfnType[x] = 0，dfn序号为x的节点是方点
    // dfnType[x] = 1，dfn序号为x的节点是圆点，位于环顶到环中重儿子的短路径侧
    // dfnType[x] = 2，dfn序号为x的节点是圆点，位于环顶到环中重儿子的长路径侧
    // dfnType[x] = 3，dfn序号为x的节点是圆点，如果不是1或2类型，那就是3类型，表示必经点
    // 必经点的情况比较多，可能是环中重儿子 或者 只做环顶 或者 不参与任何环
    // 总之无论短路径还是长路径，通过重链向上必经的点，就算3类型
    public static int[] dfnType = new int[MAXN];

    // 短路径线段树
    public static int[] all1 = new int[MAXN << 2];
    public static int[] black1 = new int[MAXN << 2];
    public static boolean[] lazy1 = new boolean[MAXN << 2];

    // 长路径线段树
    public static int[] all2 = new int[MAXN << 2];
    public static int[] black2 = new int[MAXN << 2];
    public static boolean[] lazy2 = new boolean[MAXN << 2];

    // 必经点线段树
    public static int[] all3 = new int[MAXN << 2];
    public static int[] black3 = new int[MAXN << 2];
    public static boolean[] lazy3 = new boolean[MAXN << 2];

    public static void addEdge1(int u, int v) {
        next1[++cnt1] = head1[u];
        to1[cnt1] = v;
        head1[u] = cnt1;
    }

    public static void addEdge2(int u, int v) {
        next2[++cnt2] = head2[u];
        to2[cnt2] = v;
        head2[u] = cnt2;
    }

    public static void cycleLink(int u, int v) {
        cntn++;
        cycleRoot[cntn] = u;
        addEdge2(u, cntn);
        int tmp = stasiz;
        int pop;
        int cnt = 0;
        do {
            pop = sta[tmp--];
            cnt++;
        } while (pop != v);
        cycleSum[cntn] = cnt + 1;
        do {
            pop = sta[stasiz--];
            belongCycle[pop] = cntn;
            pos[pop] = cnt--;
            addEdge2(cntn, pop);
        } while (pop != v);
    }

    public static void tarjan(int u, int preEdge) {
        dfn[u] = low[u] = ++cntd;
        sta[++stasiz] = u;
        for (int e = head1[u]; e > 0; e = next1[e]) {
            if ((e ^ 1) == preEdge) {
                continue;
            }
            int v = to1[e];
            if (dfn[v] == 0) {
                tarjan(v, e);
                if (low[v] < dfn[u]) {
                    low[u] = Math.min(low[u], low[v]);
                } else if (low[v] > dfn[u]) {
                    stasiz--;
                    addEdge2(u, v);
                } else {
                    cycleLink(u, v);
                }
            } else if (dfn[v] < dfn[u]) {
                low[u] = Math.min(low[u], dfn[v]);
            }
        }
    }

    public static void dfs1(int u, int f) {
        fa[u] = f;
        dep[u] = dep[f] + 1;
        siz[u] = 1;
        for (int e = head2[u]; e > 0; e = next2[e]) {
            int v = to2[e];
            if (v != f) {
                dfs1(v, u);
                siz[u] += siz[v];
                if (son[u] == 0 || siz[son[u]] < siz[v]) {
                    son[u] = v;
                }
            }
        }
    }

    public static void cycleDfn(int u) {
        int h = son[u];
        boolean near = pos[h] * 2 < cycleSum[u];
        cyclel[u] = cntd + 1;
        for (int e = head2[u]; e > 0; e = next2[e]) {
            int v = to2[e];
            if (v != fa[u] && v != h) {
                dfn[v] = ++cntd;
                if ((near && pos[v] < pos[h]) || (!near && pos[v] > pos[h])) {
                    dfnType[cntd] = 1;
                } else {
                    dfnType[cntd] = 2;
                }
            }
        }
        cycler[u] = cntd;
        dfn[h] = ++cntd;
    }

    public static void dfs2(int u, int t) {
        top[u] = t;
        if (dfn[u] == 0) {
            dfn[u] = ++cntd;
        }
        if (u > n) {
            cycleDfn(u);
        }
        if (u <= n) {
            treel[u] = cntd + 1;
        }
        if (son[u] != 0) {
            dfs2(son[u], t);
        }
        for (int e = head2[u]; e > 0; e = next2[e]) {
            int v = to2[e];
            if (v != fa[u] && v != son[u]) {
                dfs2(v, v);
            }
        }
        if (u <= n) {
            treer[u] = cntd;
        }
    }

    public static void up(int i) {
        all1[i] = all1[i << 1] + all1[i << 1 | 1];
        all2[i] = all2[i << 1] + all2[i << 1 | 1];
        all3[i] = all3[i << 1] + all3[i << 1 | 1];
        black1[i] = black1[i << 1] + black1[i << 1 | 1];
        black2[i] = black2[i << 1] + black2[i << 1 | 1];
        black3[i] = black3[i << 1] + black3[i << 1 | 1];
    }

    public static void reverse1(int i) {
        black1[i] = all1[i] - black1[i];
        lazy1[i] = !lazy1[i];
    }

    public static void reverse2(int i) {
        black2[i] = all2[i] - black2[i];
        lazy2[i] = !lazy2[i];
    }

    public static void reverse3(int i) {
        black3[i] = all3[i] - black3[i];
        lazy3[i] = !lazy3[i];
    }

    public static void down(int i) {
        if (lazy1[i]) {
            reverse1(i << 1);
            reverse1(i << 1 | 1);
            lazy1[i] = false;
        }
        if (lazy2[i]) {
            reverse2(i << 1);
            reverse2(i << 1 | 1);
            lazy2[i] = false;
        }
        if (lazy3[i]) {
            reverse3(i << 1);
            reverse3(i << 1 | 1);
            lazy3[i] = false;
        }
    }

    public static void build(int l, int r, int i) {
        if (l == r) {
            if (dfnType[l] == 1) {
                all1[i] = black1[i] = 1;
            }
            if (dfnType[l] == 2) {
                all2[i] = black2[i] = 1;
            }
            if (dfnType[l] == 3) {
                all3[i] = black3[i] = 1;
            }
        } else {
            int mid = (l + r) >> 1;
            build(l, mid, i << 1);
            build(mid + 1, r, i << 1 | 1);
            up(i);
        }
    }

    public static void reverse(int jobl, int jobr, int jobt, int l, int r, int i) {
        if (jobl > jobr) {
            return;
        }
        if (jobl <= l && r <= jobr) {
            if (jobt == 1 || jobt == 3) {
                reverse1(i);
            }
            if (jobt == 2 || jobt == 3) {
                reverse2(i);
            }
            reverse3(i);
            return;
        }
        down(i);
        int mid = (l + r) >> 1;
        if (jobl <= mid) {
            reverse(jobl, jobr, jobt, l, mid, i << 1);
        }
        if (mid < jobr) {
            reverse(jobl, jobr, jobt, mid + 1, r, i << 1 | 1);
        }
        up(i);
    }

    public static int query(int jobl, int jobr, int l, int r, int i) {
        if (jobl > jobr) {
            return 0;
        }
        if (jobl <= l && r <= jobr) {
            return black1[i] + black2[i] + black3[i];
        }
        down(i);
        int mid = (l + r) >> 1;
        int ans = 0;
        if (jobl <= mid) {
            ans += query(jobl, jobr, l, mid, i << 1);
        }
        if (mid < jobr) {
            ans += query(jobl, jobr, mid + 1, r, i << 1 | 1);
        }
        return ans;
    }

    public static void flipCycle(int u, int x, int op) {
        int h = son[u];
        boolean near = pos[x] * 2 < cycleSum[u];
        if ((near && op == 1) || (!near && op == 2)) {
            reverse(cyclel[u], dfn[x], 3, 1, cntn, 1);
            if (pos[h] < pos[x]) {
                reverse(dfn[h], dfn[h], 3, 1, cntn, 1);
            }
        } else {
            reverse(dfn[x], cycler[u], 3, 1, cntn, 1);
            if (pos[h] > pos[x]) {
                reverse(dfn[h], dfn[h], 3, 1, cntn, 1);
            }
        }
    }

    public static void flip(int x, int op) {
        while (x != 0) {
            int xtop = top[x];
            if (xtop > n || belongCycle[xtop] == 0) {
                reverse(dfn[xtop], dfn[x], op, 1, cntn, 1);
                x = fa[xtop];
            } else {
                if (x != xtop) {
                    reverse(dfn[son[xtop]], dfn[x], op, 1, cntn, 1);
                    x = xtop;
                }
                flipCycle(belongCycle[x], x, op);
                x = cycleRoot[belongCycle[x]];
            }
        }
    }

    public static void prepare() {
        tarjan(1, 0);
        cntd = 0;
        for (int i = 1; i <= n; i++) {
            dfn[i] = 0;
        }
        dfs1(1, 0);
        dfs2(1, 1);
        for (int i = 1; i <= n; i++) {
            if (dfnType[dfn[i]] == 0) {
                dfnType[dfn[i]] = 3;
            }
        }
        build(1, cntn, 1);
    }

    public static void main(String[] args) throws Exception {
        FastReader in = new FastReader(System.in);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        n = in.nextInt();
        m = in.nextInt();
        q = in.nextInt();
        cntn = n;
        cnt1 = 1;
        for (int i = 1, u, v; i <= m; i++) {
            u = in.nextInt();
            v = in.nextInt();
            addEdge1(u, v);
            addEdge1(v, u);
        }
        prepare();
        for (int i = 1, op, x; i <= q; i++) {
            op = in.nextInt();
            x = in.nextInt();
            if (op == 1 || op == 2) {
                flip(x, op);
            } else {
                int ans = query(dfn[x], dfn[x], 1, cntn, 1);
                ans += query(treel[x], treer[x], 1, cntn, 1);
                out.println(ans);
            }
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
