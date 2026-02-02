# 强连通分量代码模板

## 强连通分量

1. 有向图中，一些点可以彼此相互到达，这些点就构成一个强连通分量。
2. 一个强连通分量缩成一个点，原图会变成DAG（Directed Acyclic Graph），有向无环图。
3. 因为没有环，求解问题变得非常方便，最常用的求解手段就是拓扑排序。
4. 需要保证缩点之后，求解答案不受影响。
5. 求解强连通分量，掌握Tarjan算法即可。

## Tarjan算法的必备概念

1. 边分为三种类型：树边、回边、弃边。
2. `dfn[u]` ，节点 `u` 获得的 `dfn` 序号。
3. `low[u]` ， `u` 及其子树上的点，最多走一条回边，能到达的树的最上方点，是什么 `dfn` 序号。
4. `belong[u]` ，节点 `u` 分配的强连通分量序号，如果 `belong[u]` 为 `0` ，表示还没分配。
5. 遍历图的过程中，准备一个栈，用来收集遍历到的节点。
6. 遍历到的节点先进栈，一旦该节点分配了强连通分量，就从栈中弹出。

## Tarjan算法的过程（扎口袋算法）

1. 初次来到 `u` 时， `dfn[u] = low[u] = 分配的dfn序号` ，然后节点 `u` 进栈。
2. 遍历 `u` 的每个儿子 `v` ，讨论 `u` 和 `v` 之间的边是什么类型，如果是弃边，什么也不做。
3. 如果是树边，先递归计算 `low[v]` ，然后 `low[u] = min(low[u], low[v])` 。
4. 如果是回边，直接 `low[u] = min(low[u], dfn[v])` 。
5. 遍历结束后，判断 `dfn[u]` 是否等于 `low[u]` ，如果不等，代表节点 `u` 不能扎起口袋。
6. 如果相等，代表节点 `u` 能扎起口袋，栈弹出节点，直到 `u` 出现时停止，这批节点属于一个强连通分量。
7. 考察图中每个节点，一旦发现 `dfn[i] == 0` ，就从 `i` 节点开始，执行一遍Tarjan算法。
8. 最终得到图中全部强连通分量，时间复杂度 `O(n + m)` ， `n` 为节点数， `m` 为边数。

## 一个疑问

当前节点 `u` ，如果遇到了回边，那么以下两个逻辑，到底哪个是对的？

```java
for (int e = head[u]; e > 0; e = nxt[e]) {
    int v = to[e];
    if (dfn[v] == 0) { // 树边
        tarjan(v);
        low[u] = min(low[u], low[v]);
    } else {
        if (belong[v] == 0) { // 回边
            第一种写法，low[u] = min(low[u], dfn[v]);
            第二种写法，low[u] = min(low[u], low[v]);
        }
    }
}
```

两种写法的运行结果都正确，但是推荐第一种写法，第二种写法会让 `low[u]` 的含义变模糊。

## 模版

> - **Tarjan算法生成强连通分量序数组、统计强连通分量个数、生成有向无环缩点图**

```java
class Solution {

    private static class StronglyConnectedComponents {

        // 邻接表，0位置弃而不用
        private final ArrayList<ArrayList<Integer>> nextMap;
        // 节点个数
        private int n;
        // 栈
        private final int[] stack;
        // 栈顶指针
        private int stackSize;
        // 遍历时统计dfn序
        private int cnt;
        // 存储每一个节点对应的dfn序
        private final int[] dfn;
        // 每一个节点所能到达的dfn序最小的节点的dfn序
        private final int[] low;
        // 一个节点所在的强连通分量，从1开始
        private final int[] scc;
        // 遍历时记录强连通来到哪里了
        private int sccn;

        public StronglyConnectedComponents(ArrayList<ArrayList<Integer>> nextMap) {
            this.nextMap = nextMap;
            n = nextMap.size();
            stack = new int[n];
            stackSize = 0;
            dfn = new int[n];
            low = new int[n];
            cnt = 0;
            scc = new int[n];
            sccn = 0;
            n--;
            createScc();
        }

        // 从每一个点开始去构建强连通分量
        // 这样子做可以避免森林等特殊情况
        // 或者两个强连通分量A和B同时指向另外一个强连通分量C
        // 你必须遍历所有节点才能构建出A和B的另外一个
        private void createScc() {
            for (int i = 1; i <= n; i++) {
                // 已经构建的就不走tarjan算法
                if (dfn[i] == 0) {
                    tarjan(i);
                }
            }
        }

        private void tarjan(int cur) {
            // 初始时low就等于自己的dfn序
            // 深度优先搜索来到++cnt号
            low[cur] = dfn[cur] = ++cnt;
            // 当前节点入栈
            stack[stackSize++] = cur;
            // 对所有子节点做tarjan
            for (int next : nextMap.get(cur)) {
                if (dfn[next] == 0) {
                    // dfn序为0表示没有dfs过，去dfs
                    tarjan(next);
                }
                // 一个节点只能有如下三种状态
                // 没有dfs过
                // dfs了，但还在栈中，没有结算
                // 已经结算，知道该节点的强连通分量信息
                // 经过上一个if后当前节点只可能是后面两种状态
                if (scc[next] == 0) {
                    // scc[next] != 0 说明已经结算，不属于当前强连通分量
                    // scc[next] == 0 说明未结算，用我所有子节点的low更新我的low，谁小取谁
                    low[cur] = Math.min(low[cur], low[next]);
                }
            }
            // 当前dfs序等于low，说明当前节点是强连通分量的入口
            if (low[cur] == dfn[cur]) {
                // 来到一个新的强连通分量，结算
                sccn++;
                // 在栈中寻找cur
                int findCur = 0;
                // 栈内元素依次出栈，直到cur也被弹出
                // 栈内包括cur，cur以上的所有元素都属于同一个强连通分量
                do {
                    // 弹出
                    findCur = stack[--stackSize];
                    // 记录强连通分量信息
                    scc[findCur] = sccn;
                } while (findCur != cur);
            }
        }

        // 强连通分量数组
        public int[] getScc() {
            return scc;
        }

        // 强连通分量个数
        public int getSccn() {
            return sccn;
        }

        // 每一个强连通分量可以看作一个点
        // 原来的图可以浓缩成用强连通分量表示的图，强连通分量之间也用节点连接
        public ArrayList<ArrayList<Integer>> getDirectedAcyclicReducedPointGraph() {
            ArrayList<ArrayList<Integer>> dag = new ArrayList<ArrayList<Integer>>();
            for (int i = 0; i <= sccn; i++) {
                dag.add(new ArrayList<Integer>());
            }
            for (int cur = 1; cur <= n; cur++) {
                // 避免一个强连通分量A中的节点cur有多个子节点在强连通分量B中导致A有多条指向B的指针
                HashSet<Integer> isVisited = new HashSet<Integer>();
                for (int next : nextMap.get(cur)) {
                    // 不是同一个强连通分量且该强连通分量没有连接过
                    if (scc[cur] != scc[next] && !isVisited.contains(scc[next])) {
                        dag.get(scc[cur]).add(scc[next]);
                        isVisited.add(scc[next]);
                    }
                }
            }
            return dag;
        }

    }

}
```

## [B3609 [图论与代数结构 701] 强连通分量](https://www.luogu.com.cn/problem/B3609)

> - **Question 1**
>   - 给定一张 `n` 个点， `m` 条边的有向图，求出所有强连通分量，先打印强连通分量的数量，然后打印 `1` 号点所在的强连通分量，然后打印 `2` 号点所在的强连通分量，以此类推，如果当前节点属于之前的强连通分量，那就跳过，直到打印所有强连通分量，打印每个强连通分量时，按照节点编号从小到大打印。
>   - **Tips**
>     - `1 <= n <= 10^4`
>     - `1 <= m <= 10^5`

## [U224391 【模板】强连通分量](https://www.luogu.com.cn/problem/U224391)

> - **Question 2**
>   - 给定一张 `n` 个点， `m` 条边的有向图，求出所有强连通分量，先打印强连通分量的数量，每个强连通分量先打印大小，然后打印节点编号，顺序随意。
>   - **Tips**
>     - `1 <= n <= 5 * 10^4`
>     - `1 <= m <= 10^5`

## [P1726 上白泽慧音](https://www.luogu.com.cn/problem/P1726)

> - **Question 3**
>   - 一共有 `n` 个节点，给定 `m` 条边，边的格式 `a b t` ，如果 `t` 为 `1` ，表示 `a` 到 `b` 的单向边，如果 `t` 为 `2` ，表示 `a` 到 `b` 的双向边，找到图中最大的强连通分量，先打印大小，然后打印包含的节点，编号从小到大输出，如果有多个最大的强连通分量，打印字典序最小的结果。
>   - **Tips**
>     - `1 <= n <= 5 * 10^3`
>     - `0 <= m <= 5 * 10^4`

## Java

> - **Question 1**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 10001;
    public static int MAXM = 100001;
    public static int n, m;

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
    public static int[] sccArr = new int[MAXN];
    public static int[] sccl = new int[MAXN];
    public static int[] sccr = new int[MAXN];
    public static int idx;
    public static int sccCnt;

    public static boolean[] sccPrint = new boolean[MAXN];

    public static void addEdge(int u, int v) {
        nxt[++cntg] = head[u];
        to[cntg] = v;
        head[u] = cntg;
    }

    // 递归版
    public static void tarjan1(int u) {
        dfn[u] = low[u] = ++cntd;
        sta[++top] = u;
        for (int e = head[u]; e > 0; e = nxt[e]) {
            int v = to[e];
            if (dfn[v] == 0) { // 树边
                tarjan1(v);
                low[u] = Math.min(low[u], low[v]);
            } else {
                if (belong[v] == 0) { // 回边
                    low[u] = Math.min(low[u], dfn[v]);
                }
                // 如果 belong[v] != 0 就是遇到了弃边，什么也不做
            }
        }
        if (dfn[u] == low[u]) {
            sccCnt++;
            sccl[sccCnt] = idx + 1;
            int pop;
            do {
                pop = sta[top--];
                belong[pop] = sccCnt;
                sccArr[++idx] = pop;
            } while (pop != u);
            sccr[sccCnt] = idx;
        }
    }

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

    // 迭代版
    // u表示当前节点
    // e表示u当前处理的边，如果e == 0，说明所有边都处理完了
    // status的具体说明如下
    //     如果status == -1，表示u没有遍历过任何儿子
    //     如果status == 0，表示u遍历到儿子v，然后发现dfn[v] == 0
    //         并且执行完了tarjan(v)，对应递归版for循环中的第一个分支
    //     如果status == 1，表示u遍历到儿子v，然后发现dfn[v] != 0
    //         对应递归版for循环中的第二个分支
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
                    // (当前节点, 状态, 边)先进入栈
                    // (儿子节点, 状态, 边)再进入栈
                    // 那么儿子节点的tarjan过程会先执行
                    // 等到处理当前节点时，low[儿子节点]信息就生成好了
                    push(u, 0, e);
                    push(v, -1, -1);
                } else {
                    push(u, 1, e);
                }
            } else {
                if (dfn[u] == low[u]) {
                    sccCnt++;
                    sccl[sccCnt] = idx + 1;
                    int pop;
                    do {
                        pop = sta[top--];
                        belong[pop] = sccCnt;
                        sccArr[++idx] = pop;
                    } while (pop != u);
                    sccr[sccCnt] = idx;
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
        }
        for (int i = 1; i <= n; i++) {
            if (dfn[i] == 0) {
                // tarjan1(i);
                tarjan2(i);
            }
        }
        out.println(sccCnt);
        for (int i = 1; i <= sccCnt; i++) {
            Arrays.sort(sccArr, sccl[i], sccr[i] + 1);
        }
        for (int i = 1; i <= n; i++) {
            int scc = belong[i];
            if (!sccPrint[scc]) {
                sccPrint[scc] = true;
                for (int j = sccl[scc]; j <= sccr[scc]; j++) {
                    out.print(sccArr[j] + " ");
                }
                out.println();
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

> - **Question 2**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 50001;
    public static int MAXM = 100001;
    public static int n, m;

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
    public static int[] sccArr = new int[MAXN];
    public static int[] sccSiz = new int[MAXN];
    public static int[] sccl = new int[MAXN];
    public static int[] sccr = new int[MAXN];
    public static int idx;
    public static int sccCnt;

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
            sccl[sccCnt] = idx + 1;
            int pop;
            do {
                pop = sta[top--];
                belong[pop] = sccCnt;
                sccArr[++idx] = pop;
                sccSiz[sccCnt]++;
            } while (pop != u);
            sccr[sccCnt] = idx;
        }
    }

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
                    sccl[sccCnt] = idx + 1;
                    int pop;
                    do {
                        pop = sta[top--];
                        belong[pop] = sccCnt;
                        sccArr[++idx] = pop;
                        sccSiz[sccCnt]++;
                    } while (pop != u);
                    sccr[sccCnt] = idx;
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
        }
        for (int i = 1; i <= n; i++) {
            if (dfn[i] == 0) {
                // tarjan1(i);
                tarjan2(i);
            }
        }
        out.println(sccCnt);
        for (int i = 1; i <= sccCnt; i++) {
            out.print(sccSiz[i] + " ");
            for (int j = sccl[i]; j <= sccr[i]; j++) {
                out.print(sccArr[j] + " ");
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

> - **Question 3**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 5001;
    public static int MAXM = 100001;
    public static int n, m;

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
                sccSiz[sccCnt]++;
            } while (pop != u);
        }
    }

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
                        sccSiz[sccCnt]++;
                    } while (pop != u);
                }
            }
        }
    }

    public static void main(String[] args) throws Exception {
        FastReader in = new FastReader(System.in);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        n = in.nextInt();
        m = in.nextInt();
        for (int i = 1, a, b, t; i <= m; i++) {
            a = in.nextInt();
            b = in.nextInt();
            t = in.nextInt();
            if (t == 1) {
                addEdge(a, b);
            } else {
                addEdge(a, b);
                addEdge(b, a);
            }
        }
        for (int i = 1; i <= n; i++) {
            if (dfn[i] == 0) {
                // tarjan1(i);
                tarjan2(i);
            }
        }
        int largest = 0;
        for (int i = 1; i <= sccCnt; i++) {
            largest = Math.max(largest, sccSiz[i]);
        }
        out.println(largest);
        for (int i = 1; i <= n; i++) {
            if (sccSiz[belong[i]] == largest) {
                int scc = belong[i];
                for (int j = i; j <= n; j++) {
                    if (belong[j] == scc) {
                        out.print(j + " ");
                    }
                }
                break;
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
