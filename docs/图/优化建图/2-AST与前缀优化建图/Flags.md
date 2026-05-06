# Flags

## [F - Flags](https://atcoder.jp/contests/arc069/tasks/arc069_d)

> - **Question**
>   - 一共 `n` 个旗子，第 `i` 号旗子有 `a[i]` 和 `b[i]` 两个可选位置，你需要决定每个旗子，到底放在两个可选位置中的哪一个，两面旗子之间的最小距离 `d` 越大，排列看起来就越美观，找到最美观的情况，即求出所有可能的 `d` 值中的最大值。
>   - **Tips**
>     - `2 <= n <= 10^4`
>     - `1 <= a[i]、b[i] <= 10^9`

## Java

> - **二分答案 + 2-SAT + 线段树优化建图**

```java
// 除非假设x是无效的，想要进行规避，否则避免出现，x -> x的反面

// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 10001;
    public static int MAXT = 100001;
    public static int MAXE = 1000001;
    public static int n, k;
    public static int[] a = new int[MAXN];
    public static int[] b = new int[MAXN];

    // 所在的地点pos、决定的编号xid
    public static int[][] arr = new int[MAXN << 1][2];

    public static int[] ls = new int[MAXT];
    public static int[] rs = new int[MAXT];
    public static int root;
    public static int cntt;

    public static int[] head = new int[MAXT];
    public static int[] nxt = new int[MAXE];
    public static int[] to = new int[MAXE];
    public static int cntg;

    public static int[] dfn = new int[MAXT];
    public static int[] low = new int[MAXT];
    public static int cntd;

    public static int[] sta = new int[MAXT];
    public static int top;

    public static int[] belong = new int[MAXT];
    public static int sccCnt;

    public static int kth(int num) {
        int l = 1, r = k, mid, ans = k + 1;
        while (l <= r) {
            mid = (l + r) >> 1;
            if (arr[mid][0] >= num) {
                ans = mid;
                r = mid - 1;
            } else {
                l = mid + 1;
            }
        }
        return ans;
    }

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
        return i <= n ? i + n : i - n;
    }

    public static int build(int l, int r) {
        int rt;
        if (l == r) {
            rt = other(arr[l][1]);
        } else {
            rt = ++cntt;
            int mid = (l + r) >> 1;
            ls[rt] = build(l, mid);
            rs[rt] = build(mid + 1, r);
            addEdge(rt, ls[rt]);
            addEdge(rt, rs[rt]);
        }
        return rt;
    }

    public static void xToRange(int xid, int jobl, int jobr, int l, int r, int i) {
        if (jobl > jobr) {
            return;
        }
        if (jobl <= l && r <= jobr) {
            addEdge(xid, i);
        } else {
            int mid = (l + r) >> 1;
            if (jobl <= mid) {
                xToRange(xid, jobl, jobr, l, mid, ls[i]);
            }
            if (jobr > mid) {
                xToRange(xid, jobl, jobr, mid + 1, r, rs[i]);
            }
        }
    }

    public static void buildGraph(int d) {
        cntt = k;
        root = build(1, k);
        for (int i = 1; i <= k; i++) {
            int pos = arr[i][0];
            int l = kth(pos - d + 1);
            int r = kth(pos + d) - 1;
            int x = arr[i][1];
            xToRange(x, l, i - 1, 1, k, root);
            xToRange(x, i + 1, r, 1, k, root);
        }
    }

    public static void clear() {
        for (int i = 1; i <= cntt; i++) {
            head[i] = dfn[i] = belong[i] = 0;
        }
        cntt = cntg = cntd = top = sccCnt = 0;
    }

    public static boolean check(int d) {
        buildGraph(d);
        for (int i = 1; i <= n << 1; i++) {
            if (dfn[i] == 0) {
                tarjan(i);
            }
        }
        boolean check = true;
        for (int i = 1; i <= n; i++) {
            if (belong[i] == belong[i + n]) {
                check = false;
                break;
            }
        }
        clear();
        return check;
    }

    public static int compute() {
        for (int i = 1; i <= n; i++) {
            arr[i][0] = a[i];
            arr[i][1] = i;
            arr[i + n][0] = b[i];
            arr[i + n][1] = i + n;
        }
        k = n << 1;
        Arrays.sort(arr, 1, k + 1, (x, y) -> x[0] != y[0] ? x[0] - y[0] : x[1] - y[1]);
        int l = 0, r = arr[k][0], d, ans = 0;
        while (l <= r) {
            d = (l + r) >> 1;
            if (check(d)) {
                ans = d;
                l = d + 1;
            } else {
                r = d - 1;
            }
        }
        return ans;
    }

    public static void main(String[] args) throws Exception {
        FastReader in = new FastReader(System.in);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        n = in.nextInt();
        for (int i = 1; i <= n; i++) {
            a[i] = in.nextInt();
            b[i] = in.nextInt();
        }
        int ans = compute();
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
