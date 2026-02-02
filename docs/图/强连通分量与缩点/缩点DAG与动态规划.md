# 缩点DAG与动态规划

## [P3387 【模板】缩点](https://www.luogu.com.cn/problem/P3387)

> - **Question 1**
>   - 给定一张 `n` 个点， `m` 条边的有向图，每个点给定非负点权，如果重复经过一个点，点权只获得一次，找到一条路径，使得点权累加和最大，打印这个值。
>   - **Tips**
>     - `1 <= n <= 10^4`
>     - `1 <= m <= 10^5`

## [P2656 采蘑菇](https://www.luogu.com.cn/problem/P2656)

> - **Question 2**
>   - 给定一张 `n` 个点， `m` 条边的有向图，每条边有初始收益、恢复系数两种边权，初始收益为非负整数，恢复系数范围 `[0, 0.8]` ，并且最多有一位小数，比如，如果重复走过一条边，该边的初始收益为 `10` ，恢复系数为 `0.6` ，那么依次获得的收益为， `10, 6, 3, 1, 0` ，随后重复经过就没有收益了，给定起点 `s` ，找到一条必须从 `s` 出发的路径，打印收益累加和的最大值。
>   - **Tips**
>     - `1 <= n <= 8 * 10^4`
>     - `1 <= m <= 2 * 10^5`

## [P3627 [APIO2009] 抢掠计划](https://www.luogu.com.cn/problem/P3627)

> - **Question 3**
>   - 一共有 `n` 个城市，给定 `m` 条有向道路，每个城市给定拥有的钱数，给定起点城市 `s` ，给定 `p` 个有酒吧的城市，有酒吧的城市才能作为终点，路线必须从 `s` 出发到任意终点停止，重复经过城市的话，钱仅获得一次，题目保证一定存在这样的路线，打印能获得的最大钱数。
>   - **Tips**
>     - `1 <= n、m <= 5 * 10^5`

## [P1073 [NOIP 2009 提高组] 最优贸易](https://www.luogu.com.cn/problem/P1073)

> - **Question 4**
>   - 一共有 `n` 个城市，每个城市给定水晶球的销售价格，给定 `m` 条道路，格式 `a b op` ， `op` 为 `1` 代表 `a` 到 `b` 的单向路，否则表示双向路，你要从 `1` 号城市出发，可以任选道路，最终来到 `n` 号城市，沿途可以买卖一次水晶球，在途中你可以任选一座城市买入，可以立即卖出，或者在之后的任何城市卖出，如果从 `1` 号城市能到达 `n` 号城市，打印挣到的最大钱数，如果不能到达打印 `0` 。
>   - **Tips**
>     - `1 <= n <= 10^5`
>     - `1 <= m <= 5 * 10^5`

## [P2272 [ZJOI2007] 最大半连通子图](https://www.luogu.com.cn/problem/P2272)

> - **Question 5**
>   - 有向图中节点 `u` 和 `v` ，只要其中一点能到达另一点，就说两点是半连通的，如果一个有向图，任意两点都是半连通的，这样的有向图就是半连通图，有向图中的一个点集，该点集中只要某两点在原图中有边，那么这条边就保留，则可以得到一个子图，如果该子图既是半连通图，又有节点数量最多，那么这个子图就是原图的最大半连通子图，给定一张 `n` 个点， `m` 条边的有向图，打印最大半连通子图的大小，可能存在多个最大半连通子图，打印这个数量，数量对给定的数字 `x` 取余。
>   - **Tips**
>     - `1 <= n <= 10^5`
>     - `1 <= m <= 10^6`

## Java

> - **Question 1: 缩点DAG的动态规划 + 拓扑排序的写法 + 直接转移的写法**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 10001;
    public static int MAXM = 100001;
    public static int n, m;

    public static int[] arr = new int[MAXN];
    public static int[] a = new int[MAXM];
    public static int[] b = new int[MAXM];

    public static int[] head = new int[MAXN];
    public static int[] nxt = new int[MAXM];
    public static int[] to = new int[MAXM];
    public static int cntg;

    public static int[] dfn = new int[MAXN];
    public static int[] low = new int[MAXN];
    public static int cntd;

    public static int[] sta = new int[MAXN];
    public static int top;

    public static int[] belong = new int[MAXN];
    public static int[] sum = new int[MAXN];
    public static int sccCnt;

    public static int[] indegree = new int[MAXN];
    public static int[] que = new int[MAXN];
    public static int[] dp = new int[MAXN];

    public static void addEdge(int u, int v) {
        nxt[++cntg] = head[u];
        to[cntg] = v;
        head[u] = cntg;
    }

    public static void tarjan(int u) {
        dfn[u] = low[u] = ++cntd;
        sta[++top] = u;
        for (int e = head[u]; e > 0; e = nxt[e]) {
            int v = to[e];
            if (dfn[v] == 0) {
                tarjan(v);
                low[u] = Math.min(low[u], low[v]);
            } else {
                if (belong[v] == 0) {
                    low[u] = Math.min(low[u], dfn[v]);
                }
            }
        }
        if (dfn[u] == low[u]) {
            sccCnt++;
            int pop;
            do {
                pop = sta[top--];
                belong[pop] = sccCnt;
                sum[sccCnt] += arr[pop];
            } while (pop != u);
        }
    }

    public static void condense() {
        cntg = 0;
        for (int i = 1; i <= sccCnt; i++) {
            head[i] = 0;
        }
        for (int i = 1; i <= m; i++) {
            int scc1 = belong[a[i]];
            int scc2 = belong[b[i]];
            if (scc1 != scc2) {
                indegree[scc2]++;
                addEdge(scc1, scc2);
            }
        }
    }

    // 拓扑排序的写法
    public static int topo() {
        int l = 1, r = 0;
        for (int i = 1; i <= sccCnt; i++) {
            if (indegree[i] == 0) {
                dp[i] = sum[i];
                que[++r] = i;
            }
        }
        while (l <= r) {
            int u = que[l++];
            for (int e = head[u]; e > 0; e = nxt[e]) {
                int v = to[e];
                dp[v] = Math.max(dp[v], dp[u] + sum[v]);
                if (--indegree[v] == 0) {
                    que[++r] = v;
                }
            }
        }
        int ans = 0;
        for (int i = 1; i <= sccCnt; i++) {
            ans = Math.max(ans, dp[i]);
        }
        return ans;
    }

    // 直接转移的写法
    public static int dpOnDAG() {
        for (int u = sccCnt; u > 0; u--) {
            if (indegree[u] == 0) {
                dp[u] = sum[u];
            }
            for (int e = head[u]; e > 0; e = nxt[e]) {
                int v = to[e];
                dp[v] = Math.max(dp[v], dp[u] + sum[v]);
            }
        }
        int ans = 0;
        for (int u = 1; u <= sccCnt; u++) {
            ans = Math.max(ans, dp[u]);
        }
        return ans;
    }

    public static void main(String[] args) throws Exception {
        FastReader in = new FastReader(System.in);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        n = in.nextInt();
        m = in.nextInt();
        for (int i = 1; i <= n; i++) {
            arr[i] = in.nextInt();
        }
        for (int i = 1; i <= m; i++) {
            a[i] = in.nextInt();
            b[i] = in.nextInt();
            addEdge(a[i], b[i]);
        }
        for (int i = 1; i <= n; i++) {
            if (dfn[i] == 0) {
                tarjan(i);
            }
        }
        condense();
        // int ans = topo();
        int ans = dpOnDAG();
        out.println(ans);
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

> - **Question 2: 缩点DAG的动态规划 + 缩点点权的处理 + 限制起点或终点**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 80001;
    public static int MAXM = 200001;
    public static int INF = 1000000001;
    public static int n, m, s;

    public static int[] a = new int[MAXM];
    public static int[] b = new int[MAXM];
    public static int[] init = new int[MAXM];
    public static int[] recover = new int[MAXM];

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

    public static int[] belong = new int[MAXN];
    public static int sccCnt;

    public static int[] sum = new int[MAXN];
    public static int[] dp = new int[MAXN];

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

    public static void addEdge(int u, int v, int w) {
        nxt[++cntg] = head[u];
        to[cntg] = v;
        weight[cntg] = w;
        head[u] = cntg;
    }

    // 递归版，java会爆栈，C++可以通过
    public static void tarjan1(int u) {
        dfn[u] = low[u] = ++cntd;
        sta[++top] = u;
        for (int e = head[u]; e > 0; e = nxt[e]) {
            int v = to[e];
            if (dfn[v] == 0) {
                tarjan1(v);
                low[u] = Math.min(low[u], low[v]);
            } else {
                if (belong[v] == 0) {
                    low[u] = Math.min(low[u], dfn[v]);
                }
            }
        }
        if (dfn[u] == low[u]) {
            sccCnt++;
            int pop;
            do {
                pop = sta[top--];
                belong[pop] = sccCnt;
            } while (pop != u);
        }
    }

    // 迭代版
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
                }
                if (status == 1 && belong[v] == 0) {
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
            } else {
                if (dfn[u] == low[u]) {
                    sccCnt++;
                    int pop;
                    do {
                        pop = sta[top--];
                        belong[pop] = sccCnt;
                    } while (pop != u);
                }
            }
        }
    }

    public static void condense() {
        cntg = 0;
        for (int i = 1; i <= sccCnt; i++) {
            head[i] = 0;
        }
        for (int i = 1; i <= m; i++) {
            int scc1 = belong[a[i]];
            int scc2 = belong[b[i]];
            if (scc1 > 0 && scc2 > 0) {
                int val = init[i];
                int rec = recover[i];
                if (scc1 == scc2) {
                    while (val > 0) {
                        sum[scc1] += val;
                        val = val * rec / 10;
                    }
                } else {
                    addEdge(scc1, scc2, val);
                }
            }
        }
    }

    public static int dpOnDAG() {
        for (int u = 1; u <= sccCnt; u++) {
            dp[u] = -INF;
        }
        dp[belong[s]] = sum[belong[s]];
        for (int u = sccCnt; u > 0; u--) {
            for (int e = head[u]; e > 0; e = nxt[e]) {
                int v = to[e];
                int w = weight[e];
                dp[v] = Math.max(dp[v], dp[u] + w + sum[v]);
            }
        }
        int ans = 0;
        for (int u = 1; u <= sccCnt; u++) {
            ans = Math.max(ans, dp[u]);
        }
        return ans;
    }

    public static void main(String[] args) throws Exception {
        FastReader in = new FastReader(System.in);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        n = in.nextInt();
        m = in.nextInt();
        for (int i = 1; i <= m; i++) {
            a[i] = in.nextInt();
            b[i] = in.nextInt();
            init[i] = in.nextInt();
            double rec = in.nextDouble();
            recover[i] = (int) (rec * 10);
            addEdge(a[i], b[i], 0);
        }
        s = in.nextInt();
        // tarjan1(s);
        tarjan2(s);
        condense();
        int ans = dpOnDAG();
        out.println(ans);
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

        double nextDouble() throws IOException {
            int c;
            do {
                c = readByte();
            } while (c <= ' ' && c != -1);
            boolean neg = false;
            if (c == '-') {
                neg = true;
                c = readByte();
            }
            long intPart = 0;
            while (c > ' ' && c != -1 && c != '.') {
                intPart = intPart * 10 + (c - '0');
                c = readByte();
            }
            double val = (double) intPart;
            if (c == '.') {
                c = readByte();
                double base = 0.1;
                while (c > ' ' && c != -1) {
                    val += (c - '0') * base;
                    base *= 0.1;
                    c = readByte();
                }
            }
            return neg ? -val : val;
        }
    }

}
```

> - **Question 3: 缩点DAG的动态规划 + 缩点之后增加点的属性 + 限制起点或终点**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 500001;
    public static int MAXM = 500001;
    public static int INF = 1000000001;
    public static int n, m, s, p;

    public static int[] money = new int[MAXN];
    public static boolean[] isBar = new boolean[MAXN];
    public static int[] a = new int[MAXM];
    public static int[] b = new int[MAXM];

    public static int[] head = new int[MAXN];
    public static int[] nxt = new int[MAXM];
    public static int[] to = new int[MAXM];
    public static int cntg;

    public static int[] dfn = new int[MAXN];
    public static int[] low = new int[MAXN];
    public static int cntd;

    public static int[] sta = new int[MAXN];
    public static int top;

    public static int[] belong = new int[MAXN];
    public static int[] sum = new int[MAXN];
    public static boolean[] hasBar = new boolean[MAXN];
    public static int sccCnt;

    public static int[] dp = new int[MAXN];

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

    // 递归版，java会爆栈，C++可以通过
    public static void tarjan1(int u) {
        dfn[u] = low[u] = ++cntd;
        sta[++top] = u;
        for (int e = head[u]; e > 0; e = nxt[e]) {
            int v = to[e];
            if (dfn[v] == 0) {
                tarjan1(v);
                low[u] = Math.min(low[u], low[v]);
            } else {
                if (belong[v] == 0) {
                    low[u] = Math.min(low[u], dfn[v]);
                }
            }
        }
        if (dfn[u] == low[u]) {
            sccCnt++;
            sum[sccCnt] = 0;
            hasBar[sccCnt] = false;
            int pop;
            do {
                pop = sta[top--];
                belong[pop] = sccCnt;
                sum[sccCnt] += money[pop];
                hasBar[sccCnt] |= isBar[pop];
            } while (pop != u);
        }
    }

    // 迭代版
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
                }
                if (status == 1 && belong[v] == 0) {
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
            } else {
                if (dfn[u] == low[u]) {
                    sccCnt++;
                    sum[sccCnt] = 0;
                    hasBar[sccCnt] = false;
                    int pop;
                    do {
                        pop = sta[top--];
                        belong[pop] = sccCnt;
                        sum[sccCnt] += money[pop];
                        hasBar[sccCnt] |= isBar[pop];
                    } while (pop != u);
                }
            }
        }
    }

    public static void condense() {
        cntg = 0;
        for (int i = 1; i <= sccCnt; i++) {
            head[i] = 0;
        }
        for (int i = 1; i <= m; i++) {
            int scc1 = belong[a[i]];
            int scc2 = belong[b[i]];
            if (scc1 > 0 && scc2 > 0 && scc1 != scc2) {
                addEdge(scc1, scc2);
            }
        }
    }

    public static int dpOnDAG() {
        for (int u = 1; u <= sccCnt; u++) {
            dp[u] = -INF;
        }
        dp[belong[s]] = sum[belong[s]];
        for (int u = sccCnt; u > 0; u--) {
            for (int e = head[u]; e > 0; e = nxt[e]) {
                int v = to[e];
                dp[v] = Math.max(dp[v], dp[u] + sum[v]);
            }
        }
        int ans = 0;
        for (int u = 1; u <= sccCnt; u++) {
            if (hasBar[u]) {
                ans = Math.max(ans, dp[u]);
            }
        }
        return ans;
    }

    public static void main(String[] args) throws Exception {
        FastReader in = new FastReader(System.in);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        n = in.nextInt();
        m = in.nextInt();
        for (int i = 1; i <= m; i++) {
            a[i] = in.nextInt();
            b[i] = in.nextInt();
            addEdge(a[i], b[i]);
        }
        for (int i = 1; i <= n; i++) {
            money[i] = in.nextInt();
        }
        s = in.nextInt();
        p = in.nextInt();
        for (int i = 1, x; i <= p; i++) {
            x = in.nextInt();
            isBar[x] = true;
        }
        // tarjan1(s);
        tarjan2(s);
        condense();
        int ans = dpOnDAG();
        out.println(ans);
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

> - **Question 4: 缩点DAG的动态规划 + 限制起点或终点**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 100001;
    public static int MAXM = 500001;
    public static int INF = 1000000001;
    public static int n, m;

    public static int[] val = new int[MAXN];
    public static int[] a = new int[MAXM];
    public static int[] b = new int[MAXM];

    public static int[] head = new int[MAXN];
    public static int[] nxt = new int[MAXM << 1];
    public static int[] to = new int[MAXM << 1];
    public static int cntg;

    public static int[] dfn = new int[MAXN];
    public static int[] low = new int[MAXN];
    public static int cntd;

    public static int[] sta = new int[MAXN];
    public static int top;

    public static int[] belong = new int[MAXN];
    public static int[] sccMin = new int[MAXN];
    public static int[] sccMax = new int[MAXN];
    public static int sccCnt;

    public static int[] premin = new int[MAXN];
    public static int[] dp = new int[MAXN];

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

    // 递归版，java会爆栈，C++可以通过
    public static void tarjan1(int u) {
        dfn[u] = low[u] = ++cntd;
        sta[++top] = u;
        for (int e = head[u]; e > 0; e = nxt[e]) {
            int v = to[e];
            if (dfn[v] == 0) {
                tarjan1(v);
                low[u] = Math.min(low[u], low[v]);
            } else {
                if (belong[v] == 0) {
                    low[u] = Math.min(low[u], dfn[v]);
                }
            }
        }
        if (dfn[u] == low[u]) {
            sccCnt++;
            sccMin[sccCnt] = INF;
            sccMax[sccCnt] = -INF;
            int pop;
            do {
                pop = sta[top--];
                belong[pop] = sccCnt;
                sccMin[sccCnt] = Math.min(sccMin[sccCnt], val[pop]);
                sccMax[sccCnt] = Math.max(sccMax[sccCnt], val[pop]);
            } while (pop != u);
        }
    }

    // 迭代版
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
                }
                if (status == 1 && belong[v] == 0) {
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
            } else {
                if (dfn[u] == low[u]) {
                    sccCnt++;
                    sccMin[sccCnt] = INF;
                    sccMax[sccCnt] = -INF;
                    int pop;
                    do {
                        pop = sta[top--];
                        belong[pop] = sccCnt;
                        sccMin[sccCnt] = Math.min(sccMin[sccCnt], val[pop]);
                        sccMax[sccCnt] = Math.max(sccMax[sccCnt], val[pop]);
                    } while (pop != u);
                }
            }
        }
    }

    public static void condense() {
        cntg = 0;
        for (int i = 1; i <= sccCnt; i++) {
            head[i] = 0;
        }
        for (int i = 1; i <= m; i++) {
            int scc1 = belong[a[i]];
            int scc2 = belong[b[i]];
            if (scc1 > 0 && scc2 > 0 && scc1 != scc2) {
                addEdge(scc1, scc2);
            }
        }
    }

    public static int dpOnDAG() {
        for (int u = 1; u <= sccCnt; u++) {
            premin[u] = INF;
            dp[u] = -INF;
        }
        int s = belong[1];
        premin[s] = sccMin[s];
        dp[s] = sccMax[s] - sccMin[s];
        for (int u = sccCnt; u > 0; u--) {
            for (int e = head[u]; e > 0; e = nxt[e]) {
                int v = to[e];
                premin[v] = Math.min(premin[v], Math.min(premin[u], sccMin[v]));
                dp[v] = Math.max(dp[v], Math.max(dp[u], sccMax[v] - premin[v]));
            }
        }
        return dp[belong[n]];
    }

    public static void main(String[] args) throws Exception {
        FastReader in = new FastReader(System.in);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        n = in.nextInt();
        m = in.nextInt();
        for (int i = 1; i <= n; i++) {
            val[i] = in.nextInt();
        }
        for (int i = 1, op; i <= m; i++) {
            a[i] = in.nextInt();
            b[i] = in.nextInt();
            op = in.nextInt();
            if (op == 1) {
                addEdge(a[i], b[i]);
            } else {
                addEdge(a[i], b[i]);
                addEdge(b[i], a[i]);
            }
        }
        // tarjan1(1);
        tarjan2(1);
        if (belong[n] == 0) {
            out.println(0);
        } else {
            condense();
            int ans = dpOnDAG();
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

> - **Question 5: 缩点DAG的动态规划 + 关于重边的分析 + 判断重边省空间的写法**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 100001;
    public static int MAXM = 1000001;
    public static int n, m, x;
    public static int[] a = new int[MAXM];
    public static int[] b = new int[MAXM];

    public static int[] head = new int[MAXN];
    public static int[] nxt = new int[MAXM];
    public static int[] to = new int[MAXM];
    public static int cntg;

    public static int[] dfn = new int[MAXN];
    public static int[] low = new int[MAXN];
    public static int cntd;

    public static int[] sta = new int[MAXN];
    public static int top;

    public static int[] belong = new int[MAXN];
    public static int[] sccSiz = new int[MAXN];
    public static int sccCnt;

    public static long[] edgeArr = new long[MAXM];
    public static int cnte;

    public static int[] indegree = new int[MAXN];
    public static int[] dpSum = new int[MAXN];
    public static int[] dpCnt = new int[MAXN];

    public static int ans1, ans2;

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

    // 递归版，java会爆栈，C++可以通过
    public static void tarjan1(int u) {
        dfn[u] = low[u] = ++cntd;
        sta[++top] = u;
        for (int e = head[u]; e > 0; e = nxt[e]) {
            int v = to[e];
            if (dfn[v] == 0) {
                tarjan1(v);
                low[u] = Math.min(low[u], low[v]);
            } else {
                if (belong[v] == 0) {
                    low[u] = Math.min(low[u], dfn[v]);
                }
            }
        }
        if (dfn[u] == low[u]) {
            sccCnt++;
            sccSiz[sccCnt] = 0;
            int pop;
            do {
                pop = sta[top--];
                belong[pop] = sccCnt;
                sccSiz[sccCnt]++;
            } while (pop != u);
        }
    }

    // 迭代版
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
                }
                if (status == 1 && belong[v] == 0) {
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
            } else {
                if (dfn[u] == low[u]) {
                    sccCnt++;
                    sccSiz[sccCnt] = 0;
                    int pop;
                    do {
                        pop = sta[top--];
                        belong[pop] = sccCnt;
                        sccSiz[sccCnt]++;
                    } while (pop != u);
                }
            }
        }
    }

    public static void condense() {
        cntg = 0;
        for (int i = 1; i <= sccCnt; i++) {
            head[i] = 0;
        }
        for (int i = 1; i <= m; i++) {
            int scc1 = belong[a[i]];
            int scc2 = belong[b[i]];
            if (scc1 != scc2) {
                edgeArr[++cnte] = ((long) scc1 << 32) | scc2;
            }
        }
        Arrays.sort(edgeArr, 1, cnte + 1);
        long pre = 0, cur;
        for (int i = 1; i <= cnte; i++) {
            cur = edgeArr[i];
            if (cur != pre) {
                int scc1 = (int) (cur >>> 32);
                int scc2 = (int) (cur & 0xffffffffL);
                indegree[scc2]++;
                addEdge(scc1, scc2);
                pre = cur;
            }
        }
    }

    public static void dpOnDAG() {
        for (int u = sccCnt; u > 0; u--) {
            if (indegree[u] == 0) {
                dpSum[u] = sccSiz[u];
                dpCnt[u] = 1;
            }
            for (int e = head[u]; e > 0; e = nxt[e]) {
                int v = to[e];
                if (dpSum[v] < dpSum[u] + sccSiz[v]) {
                    dpSum[v] = dpSum[u] + sccSiz[v];
                    dpCnt[v] = dpCnt[u];
                } else if (dpSum[v] == dpSum[u] + sccSiz[v]) {
                    dpCnt[v] = (dpCnt[v] + dpCnt[u]) % x;
                }
            }
        }
        ans1 = ans2 = 0;
        for (int i = 1; i <= sccCnt; i++) {
            if (dpSum[i] > ans1) {
                ans1 = dpSum[i];
                ans2 = dpCnt[i];
            } else if (dpSum[i] == ans1) {
                ans2 = (ans2 + dpCnt[i]) % x;
            }
        }
    }

    public static void main(String[] args) throws Exception {
        FastReader in = new FastReader(System.in);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        n = in.nextInt();
        m = in.nextInt();
        x = in.nextInt();
        for (int i = 1; i <= m; i++) {
            a[i] = in.nextInt();
            b[i] = in.nextInt();
            addEdge(a[i], b[i]);
        }
        for (int i = 1; i <= n; i++) {
            if (dfn[i] == 0) {
                // tarjan1(i);
                tarjan2(i);
            }
        }
        condense();
        dpOnDAG();
        out.println(ans1);
        out.println(ans2);
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
