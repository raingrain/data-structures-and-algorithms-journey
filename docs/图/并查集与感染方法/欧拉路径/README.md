# 欧拉路径

通俗理解就是一笔画，每条边必须经过一次，必须经过所有的边，所形成的路，起点和终点是同一个点叫欧拉回路（欧拉图），起点和终点不是同一个点叫欧拉路径（半欧拉图）。

## 有向图存在欧拉回路的条件

1. 忽略入度和出度都为 `0` 的点后，剩下每个点满足 `入度 = 出度` 。
2. 剩余图是强连通的（任意两点可以互相到达）。

## 构造性的证明

1. 从任意点 `s` 出发，如果遇到出边就选一条继续走，最终走到无法再走，一定会回到 `s` ，认为生成了主线。
2. 如果有剩余的边没有走到，那么该边必然可被主线上的某点 `x` 找到，因为图是强连通的。
3. 从点 `x` 出发，继续仿照步骤 `1` ，继续走没有走过的边，最终一定回到 `x` ，认为生成了支线。
4. 把 `x` 走出的支线，插入到主线里，可以拼成一个欧拉回路。
5. 主线不停插入支线，最终可以得到整个欧拉回路，这就是Hierholzer算法。

## 有向图存在欧拉路径的条件

1. 忽略入度和出度都为 `0` 的点后，除了起点和终点外，剩下每个点满足 `入度 = 出度` 。
2. `起点的出度 = 入度 + 1` 。
3. `终点的入度 = 出度 + 1` 。
4. 有且仅有一个起点和一个终点。
5. 如果终点向起点再连一条有向边，接下来判定是否有欧拉回路即可。

## 对于无向图

1. 忽略度为 `0` 的点，剩余的图全连通 。
2. 统计度为奇数的点的个数，记为 `odd` 。
3. 如果 `odd` 为 `0` ，存在欧拉回路。
4. 如果 `odd` 为 `2` ，存在欧拉路径。
5. 否则不存在欧拉回路或者欧拉路径。
6. 证明过程与有向图的证明过程相似。

## 欧拉路径/欧拉回路的判定和生成

1. 根据条件找到起点。
2. 从起点开始，通过Hierholzer算法生成路径。
3. 连通性的判定可以借助Hierholzer算法，看看能否走完所有的边，无需借助并查集等更复杂的手段。

## Hierholzer算法，希尔霍尔策算法，有向图生成欧拉路径的代码，包含当前弧优化

```java
void euler(int u) {
    for (int e = cur[u]; e > 0; e = cur[u]) {
        cur[u] = nxt[e];
        euler(to[e]);
    }
    path[++cntp] = u;
}
```

## 一些提醒

1. 判定分两步，能否找到起点，生成的路径能否包含所有边。
2. 最终生成的路径数组，倒序输出才是真正的欧拉路径/欧拉回路。
3. 对于无向图，根据无向边的序号是否已经使用，来执行Hierholzer算法。
4. 欧拉路径/欧拉回路，有时要求满足最小的字典序等其他要求，此时每个点的边集进行排序即可。
5. Hierholzer算法的时间复杂度 `O(n + m)` ，如果有排序过程，复杂度再乘 `O(log m)` 。

## [P7771 【模板】欧拉路径](https://www.luogu.com.cn/problem/P7771)

> - **Question**
>   - 图中有 `n` 个点， `m` 条有向边，每条边给出两个端点，如果存在欧拉路径，输出字典序最小的结果，如果不存在打印 `No` 。
>   - **Tips**
>     - `1 <= n <= 10^5`
>     - `1 <= m <= 2 * 10^5`

## Java

> - **Question**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static class EdgeCmp implements Comparator<int[]> {
        @Override
        public int compare(int[] e1, int[] e2) {
            return e1[0] != e2[0] ? (e1[0] - e2[0]) : (e1[1] - e2[1]);
        }
    }

    public static int MAXN = 100001;
    public static int MAXM = 200002;
    public static int n, m;
    public static int[][] edgeArr = new int[MAXM][2];

    public static int[] head = new int[MAXN];
    public static int[] nxt = new int[MAXM];
    public static int[] to = new int[MAXM];
    public static int cntg;

    public static int[] cur = new int[MAXN];
    public static int[] outDeg = new int[MAXN];
    public static int[] inDeg = new int[MAXN];

    public static int[] path = new int[MAXM];
    public static int cntp;

    public static void addEdge(int u, int v) {
        nxt[++cntg] = head[u];
        to[cntg] = v;
        head[u] = cntg;
    }

    public static void connect() {
        Arrays.sort(edgeArr, 1, m + 1, new EdgeCmp());
        for (int l = 1, r = 1; l <= m; l = ++r) {
            while (r + 1 <= m && edgeArr[l][0] == edgeArr[r + 1][0]) {
                r++;
            }
            for (int i = r, u, v; i >= l; i--) {
                u = edgeArr[i][0];
                v = edgeArr[i][1];
                outDeg[u]++;
                inDeg[v]++;
                addEdge(u, v);
            }
        }
        for (int i = 1; i <= n; i++) {
            cur[i] = head[i];
        }
    }

    // 有向图中找到一个起点，去生成欧拉回路 或者 欧拉路径
    public static int directedStart() {
        int start = -1, end = -1;
        for (int i = 1; i <= n; i++) {
            int v = outDeg[i] - inDeg[i];
            if (v < -1 || v > 1 || (v == 1 && start != -1) || (v == -1 && end != -1)) {
                return -1;
            }
            if (v == 1) {
                start = i;
            }
            if (v == -1) {
                end = i;
            }
        }
        if ((start == -1) ^ (end == -1)) {
            return -1;
        }
        if (start != -1) {
            return start;
        }
        for (int i = 1; i <= n; i++) {
            if (outDeg[i] > 0) {
                return i;
            }
        }
        return -1;
    }

    // Hierholzer算法递归版，java会爆栈，C++可以通过
    public static void euler1(int u) {
        for (int e = cur[u]; e > 0; e = cur[u]) {
            cur[u] = nxt[e];
            euler1(to[e]);
        }
        path[++cntp] = u;
    }

    // Hierholzer算法迭代版
    public static int[] sta = new int[MAXM];
    public static int top;

    public static void euler2(int node) {
        top = 0;
        sta[++top] = node;
        while (top > 0) {
            int u = sta[top--];
            int e = cur[u];
            if (e != 0) {
                cur[u] = nxt[e];
                sta[++top] = u;
                sta[++top] = to[e];
            } else {
                path[++cntp] = u;
            }
        }
    }

    public static void main(String[] args) throws Exception {
        FastReader in = new FastReader(System.in);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        n = in.nextInt();
        m = in.nextInt();
        for (int i = 1; i <= m; i++) {
            edgeArr[i][0] = in.nextInt();
            edgeArr[i][1] = in.nextInt();
        }
        connect();
        int start = directedStart();
        if (start == -1) {
            out.println("No");
        } else {
            // euler1(start);
            euler2(start);
            if (cntp != m + 1) {
                out.println("No");
            } else {
                for (int i = cntp; i >= 1; i--) {
                    out.print(path[i] + " ");
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
