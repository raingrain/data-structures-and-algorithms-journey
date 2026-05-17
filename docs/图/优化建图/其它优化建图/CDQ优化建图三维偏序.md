# CDQ优化建图三维偏序

> - **Question**
>   - 一共 `n` 个点，每个点给定三个点权， `a[i], b[i], c[i]` ，初始时没有边，一共 `m` 条操作，格式 `x w` ，每个满足如下要求的点，都向 `x` 连权值为 `w` 的边， `三种点权 <= x对应的点权` ，这样的点就算满足要求，注意x自己也是达标的，建好图之后可以测试图的任何算法，比如 `dijkstra` 算法求最短路，对数器验证。

## Java

> - **组内连边 + CDQ分治 + 树状数组优化建图**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 101;
    public static int MAXM = 1001;
    public static int MAXV = 10001;
    public static int MAXT = 100001;
    public static int MAXE = 100001;
    public static int n, m, maxc, cntt;
    public static int[] a = new int[MAXN];
    public static int[] b = new int[MAXN];
    public static int[] c = new int[MAXN];

    // i、a、b、c
    public static int[][] arr = new int[MAXN][4];
    public static int cnta;

    // 建图
    public static int[] headg = new int[MAXT];
    public static int[] nextg = new int[MAXE];
    public static int[] tog = new int[MAXE];
    public static int[] weightg = new int[MAXE];
    public static int cntg;

    // 每个小组的操作列表
    public static int[] headp = new int[MAXT];
    public static int[] nextp = new int[MAXM << 1];
    public static int[] top = new int[MAXM << 1];
    public static int[] weightp = new int[MAXM << 1];
    public static int cntp;

    // 树状数组
    public static int[] tree = new int[MAXV];

    public static void addEdge(int u, int v, int w) {
        nextg[++cntg] = headg[u];
        tog[cntg] = v;
        weightg[cntg] = w;
        headg[u] = cntg;
    }

    public static void addOp(int x, int u, int w) {
        nextp[++cntp] = headp[x];
        top[cntp] = u;
        weightp[cntp] = w;
        headp[x] = cntp;
    }

    public static int lowbit(int i) {
        return i & -i;
    }

    public static void add(int i, int x) {
        while (i <= maxc) {
            int pre = tree[i];
            int cur = ++cntt;
            if (pre != 0) {
                addEdge(pre, cur, 0);
            }
            addEdge(x, cur, 0);
            tree[i] = cur;
            i += lowbit(i);
        }
    }

    public static void rangeToX(int i, int x, int w) {
        while (i > 0) {
            if (tree[i] > 0) {
                addEdge(tree[i], x, w);
            }
            i -= lowbit(i);
        }
    }

    public static void clear(int val) {
        while (val <= maxc) {
            tree[val] = 0;
            val += lowbit(val);
        }
    }

    public static void merge(int l, int m, int r) {
        int p1, p2;
        for (p1 = l - 1, p2 = m + 1; p2 <= r; p2++) {
            while (p1 + 1 <= m && arr[p1 + 1][2] <= arr[p2][2]) {
                p1++;
                add(arr[p1][3], arr[p1][0]);
            }
            for (int e = headp[arr[p2][0]]; e > 0; e = nextp[e]) {
                int u = top[e];
                int w = weightp[e];
                rangeToX(arr[p2][3], u, w);
            }
        }
        for (int i = l; i <= p1; i++) {
            clear(arr[i][3]);
        }
        Arrays.sort(arr, l, r + 1, (x, y) -> x[2] - y[2]);
    }

    public static void cdq(int l, int r) {
        if (l == r) {
            return;
        }
        int mid = (l + r) / 2;
        cdq(l, mid);
        cdq(mid + 1, r);
        merge(l, mid, r);
    }

    public static void buildGraph() {
        for (int i = 1; i <= n; i++) {
            arr[i][0] = i;
            arr[i][1] = a[i];
            arr[i][2] = b[i];
            arr[i][3] = c[i];
            maxc = Math.max(maxc, c[i]);
        }
        Arrays.sort(arr, 1, n + 1, (x, y) -> x[1] != y[1] ? x[1] - y[1] : x[2] != y[2] ? x[2] - y[2] : x[3] - y[3]);
        cntt = n;
        for (int l = 1, r = 1; l <= n; l = ++r) {
            int a = arr[l][1];
            int b = arr[l][2];
            int c = arr[l][3];
            while (r + 1 <= n && a == arr[r + 1][1] && b == arr[r + 1][2] && c == arr[r + 1][3]) {
                r++;
            }
            int x = ++cntt;
            for (int i = l; i <= r; i++) {
                int u = arr[i][0];
                addEdge(u, x, 0);
                for (int e = headp[u]; e > 0; e = nextp[e]) {
                    addEdge(x, u, weightp[e]);
                    addOp(x, u, weightp[e]);
                }
            }
            arr[++cnta][0] = x;
            arr[cnta][1] = a;
            arr[cnta][2] = b;
            arr[cnta][3] = c;
        }
        cdq(1, cnta);
    }

    public static int MAX_2 = 100001;
    public static int[] head_2 = new int[MAX_2];
    public static int[] nxt_2 = new int[MAX_2];
    public static int[] to_2 = new int[MAX_2];
    public static int[] weight_2 = new int[MAX_2];
    public static int cnt_2;

    public static void addEdge_2(int u, int v, int w) {
        nxt_2[++cnt_2] = head_2[u];
        to_2[cnt_2] = v;
        weight_2[cnt_2] = w;
        head_2[u] = cnt_2;
    }

    public static void link(int x, int w) {
        for (int i = 1; i <= n; i++) {
            if (a[i] <= a[x] && b[i] <= b[x] && c[i] <= c[x]) {
                addEdge_2(i, x, w);
            }
        }
    }

    public static int INF = 1000000001;
    public static int[] dist = new int[MAXT];
    public static boolean[] vis = new boolean[MAXT];
    public static PriorityQueue<int[]> heap = new PriorityQueue<>((x, y) -> x[1] - y[1]);

    public static int dijkstra(int s, int t) {
        for (int i = 1; i <= cntt; i++) {
            dist[i] = INF;
            vis[i] = false;
        }
        heap.clear();
        dist[s] = 0;
        heap.add(new int[]{s, 0});
        while (!heap.isEmpty()) {
            int[] cur = heap.poll();
            int u = cur[0];
            int d = cur[1];
            if (u == t) {
                return d;
            }
            if (!vis[u]) {
                vis[u] = true;
                for (int e = headg[u]; e > 0; e = nextg[e]) {
                    int v = tog[e];
                    int w = weightg[e];
                    if (!vis[v] && dist[v] > d + w) {
                        dist[v] = d + w;
                        heap.add(new int[]{v, dist[v]});
                    }
                }
            }
        }
        return -1;
    }

    public static int dijkstra2(int s, int t) {
        for (int i = 1; i <= n; i++) {
            dist[i] = INF;
            vis[i] = false;
        }
        heap.clear();
        dist[s] = 0;
        heap.add(new int[]{s, 0});
        while (!heap.isEmpty()) {
            int[] cur = heap.poll();
            int u = cur[0];
            int d = cur[1];
            if (u == t) {
                return d;
            }
            if (!vis[u]) {
                vis[u] = true;
                for (int e = head_2[u]; e > 0; e = nxt_2[e]) {
                    int v = to_2[e];
                    int w = weight_2[e];
                    if (!vis[v] && dist[v] > d + w) {
                        dist[v] = d + w;
                        heap.add(new int[]{v, dist[v]});
                    }
                }
            }
        }
        return -1;
    }

    public static int random(int v) {
        return (int) (Math.random() * v) + 1;
    }

    public static void clear() {
        for (int i = 1; i <= cntt; i++) {
            headg[i] = headp[i] = 0;
        }
        for (int i = 1; i <= n; i++) {
            head_2[i] = 0;
        }
        maxc = cnta = cntg = cntp = cntt = cnt_2 = 0;
    }

    public static void main(String[] args) {
        System.out.println("cdq优化建图，三维偏序");
        System.out.println("=============");
        n = 100;
        m = 1000;
        int valMax = 10;
        int weightMax = 10000;
        int round = 20;
        for (int t = 1; t <= round; t++) {
            System.out.println("第" + t + "轮");
            System.out.println("测试开始");
            for (int i = 1; i <= n; i++) {
                a[i] = random(valMax);
                b[i] = random(valMax);
                c[i] = random(valMax);
            }
            for (int i = 1; i <= m; i++) {
                int x = random(n);
                int w = random(weightMax);
                addOp(x, x, w);
                link(x, w);
            }
            buildGraph();
            for (int x = 1; x <= n; x++) {
                for (int y = 1; y <= n; y++) {
                    int ans1 = dijkstra(x, y);
                    int ans2 = dijkstra2(x, y);
                    if (ans1 != ans2) {
                        System.out.println("出错了!");
                    }
                }
            }
            clear();
            System.out.println("测试结束");
            System.out.println("=======");
        }
    }

}
```
