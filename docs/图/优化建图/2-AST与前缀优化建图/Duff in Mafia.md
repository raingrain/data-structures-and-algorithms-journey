# Duff in Mafia

## [D. Duff in Mafia](https://codeforces.com/problemset/problem/587/D)

> - **Question**
>   - 一共 `n` 个点， `m` 条无向边，每条边给定端点 `x` 、端点 `y` 、颜色 `c` 、破坏时间 `t` ，图中保证没有自环，你可以选择一些边作为摧毁边，剩下的边作为幸存边，摧毁的方式是并行的，所以 `摧毁耗时 = max(所有摧毁边的破坏时间)` ，要求任意两条摧毁边不能在图中相邻，任意两条同色的幸存边也不能相邻，满足要求的前提下，摧毁耗时想尽可能的小，请找到可行的摧毁边集，不存在方案打印 `No` ，存在方案打印 `Yes` ，然后依次打印如下内容，摧毁耗时、摧毁边集的大小、摧毁边集每条边的序号，任何一个方案皆可。
>   - **Tips**
>     - `1 <= n、m <= 5 * 10^4`
>     - `1 <= c、t <= 10^9`

## Java

> - **二分答案 + 2-SAT + 前后缀优化建图**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXM = 50001;
    public static int MAXT = 500001;
    public static int MAXE = 1000001;
    public static int n, m, k, maxt, cntt;

    // 边的序号、所属节点、边的颜色
    public static int[][] arr = new int[MAXM << 1][3];

    // 边的破坏时间
    public static int[] destroyTime = new int[MAXM];

    public static int[] head = new int[MAXT];
    public static int[] nxt = new int[MAXE];
    public static int[] to = new int[MAXE];
    public static int cntg;

    public static int[] group = new int[MAXM];
    public static int gsiz;

    public static int[] dfn = new int[MAXT];
    public static int[] low = new int[MAXT];
    public static int cntd;

    public static int[] sta = new int[MAXT];
    public static int top;

    public static int[] belong = new int[MAXT];
    public static int sccCnt;

    public static int ansTime;
    public static int ansSize;
    public static int[] ansArr = new int[MAXM];

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
            } while (pop != u);
        }
    }

    public static int other(int i) {
        return i <= m ? i + m : i - m;
    }

    // 前后缀优化建图
    public static void groupLink() {
        if (gsiz > 1) {
            cntt++;
            addEdge(cntt, other(group[1]));
            for (int i = 2; i <= gsiz; i++) {
                cntt++;
                addEdge(cntt, other(group[i]));
                addEdge(group[i], cntt - 1);
                addEdge(cntt, cntt - 1);
            }
            cntt++;
            addEdge(cntt, other(group[gsiz]));
            for (int i = gsiz - 1; i >= 1; i--) {
                cntt++;
                addEdge(cntt, other(group[i]));
                addEdge(group[i], cntt - 1);
                addEdge(cntt, cntt - 1);
            }
        }
    }

    public static void buildGraph(int limit) {
        cntt = m << 1;
        for (int i = 1; i <= m; i++) {
            if (destroyTime[i] > limit) {
                addEdge(i, i + m);
            }
        }
        for (int l = 1, r = 1; l <= k; l = ++r) {
            int curx = arr[l][1];
            while (r + 1 <= k && curx == arr[r + 1][1]) {
                r++;
            }
            gsiz = 0;
            for (int i = l; i <= r; i++) {
                group[++gsiz] = arr[i][0];
            }
            groupLink();
        }
        for (int l = 1, r = 1; l <= k; l = ++r) {
            int curx = arr[l][1];
            int curc = arr[l][2];
            while (r + 1 <= k && curx == arr[r + 1][1] && curc == arr[r + 1][2]) {
                r++;
            }
            gsiz = 0;
            for (int i = l; i <= r; i++) {
                group[++gsiz] = other(arr[i][0]);
            }
            groupLink();
        }
    }

    public static void clear() {
        for (int i = 1; i <= cntt; i++) {
            head[i] = dfn[i] = belong[i] = 0;
        }
        cntt = cntg = cntd = top = sccCnt = 0;
    }

    public static boolean getAns(int limit) {
        buildGraph(limit);
        for (int i = 1; i <= m << 1; i++) {
            if (dfn[i] == 0) {
                tarjan(i);
            }
        }
        boolean check = true;
        for (int i = 1; i <= m; i++) {
            if (belong[i] == belong[i + m]) {
                check = false;
                break;
            }
        }
        if (check) {
            ansTime = ansSize = 0;
            for (int i = 1; i <= m; i++) {
                if (belong[i] < belong[i + m]) {
                    ansTime = Math.max(ansTime, destroyTime[i]);
                    ansArr[++ansSize] = i;
                }
            }
        }
        clear();
        return check;
    }

    public static boolean compute() {
        Arrays.sort(arr, 1, k + 1, (a, b) -> a[1] != b[1] ? a[1] - b[1] : a[2] - b[2]);
        int l = 1, r = maxt, mid;
        boolean hasAns = false;
        while (l <= r) {
            mid = (l + r) >> 1;
            if (getAns(mid)) {
                hasAns = true;
                r = mid - 1;
            } else {
                l = mid + 1;
            }
        }
        return hasAns;
    }

    public static void main(String[] args) throws Exception {
        FastReader in = new FastReader(System.in);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        n = in.nextInt();
        m = in.nextInt();
        maxt = 0;
        for (int i = 1, x, y, c, t; i <= m; i++) {
            x = in.nextInt();
            y = in.nextInt();
            c = in.nextInt();
            t = in.nextInt();
            arr[++k][0] = i;
            arr[k][1] = x;
            arr[k][2] = c;
            arr[++k][0] = i;
            arr[k][1] = y;
            arr[k][2] = c;
            destroyTime[i] = t;
            maxt = Math.max(maxt, t);
        }
        boolean check = compute();
        if (check) {
            out.println("Yes");
            out.println(ansTime + " " + ansSize);
            for (int i = 1; i <= ansSize; i++) {
                out.print(ansArr[i] + " ");
            }
        } else {
            out.println("No");
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
