# Nearby Cows G

## [P3047 [USACO12FEB] Nearby Cows G](https://www.luogu.com.cn/problem/P3047)

> - ***Question***
>   - 给定一棵 `n` 个点的树，每个点有点权。到达每个节点的距离不超过 `k` 的节点就有若干个。把这些节点权值加起来，就是该点不超过距离 `k` 的点权和。打印每个节点不超过距离k的点权和。注意 `k` 并不大。

---

## *Java*

> - ***递归版***

```java
import java.io.*;
import java.util.Arrays;

public class Main {

    public static int MAXN = 100001;

    public static int MAXK = 21;

    public static int n;

    public static int k;

    public static int[] head = new int[MAXN];

    public static int[] next = new int[MAXN << 1];

    public static int[] to = new int[MAXN << 1];

    public static int cnt;

    // sum[u][i] : 以u为头的子树内，距离为i的节点权值和
    public static int[][] sum = new int[MAXN][MAXK];

    // dp[u][i] : 以u做根，整棵树上，距离为i的节点权值和
    public static int[][] dp = new int[MAXN][MAXK];

    public static void build() {
        cnt = 1;
        Arrays.fill(head, 1, n + 1, 0);
    }

    public static void addEdge(int u, int v) {
        next[cnt] = head[u];
        to[cnt] = v;
        head[u] = cnt++;
    }

    public static void dfs1(int u, int f) {
        for (int e = head[u], v; e != 0; e = next[e]) {
            v = to[e];
            if (v != f) {
                dfs1(v, u);
            }
        }
        for (int e = head[u], v; e != 0; e = next[e]) {
            v = to[e];
            if (v != f) {
                for (int j = 1; j <= k; j++) {
                    sum[u][j] += sum[v][j - 1];
                }
            }
        }
    }

    public static void dfs2(int u, int f) {
        for (int e = head[u], v; e != 0; e = next[e]) {
            v = to[e];
            if (v != f) {
                dp[v][0] = sum[v][0];
                dp[v][1] = sum[v][1] + dp[u][0];
                for (int i = 2; i <= k; i++) {
                    dp[v][i] = sum[v][i] + dp[u][i - 1] - sum[v][i - 2];
                }
                dfs2(v, u);
            }
        }
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        n = (int) in.nval;
        build();
        in.nextToken();
        k = (int) in.nval;
        for (int i = 1, u, v; i < n; i++) {
            in.nextToken();
            u = (int) in.nval;
            in.nextToken();
            v = (int) in.nval;
            addEdge(u, v);
            addEdge(v, u);
        }
        for (int i = 1; i <= n; i++) {
            in.nextToken();
            sum[i][0] = (int) in.nval;
        }
        dfs1(1, 0);
        for (int i = 0; i <= k; i++) {
            dp[1][i] = sum[1][i];
        }
        dfs2(1, 0);
        for (int i = 1, ans; i <= n; i++) {
            ans = 0;
            for (int j = 0; j <= k; j++) {
                ans += dp[i][j];
            }
            out.println(ans);
        }
        out.flush();
        out.close();
        br.close();
    }

}
```

> - ***迭代版***

```java
import java.io.*;
import java.util.Arrays;

public class Main {

    public static int MAXN = 100001;

    public static int MAXK = 21;

    public static int n;

    public static int k;

    public static int[] head = new int[MAXN];

    public static int[] next = new int[MAXN << 1];

    public static int[] to = new int[MAXN << 1];

    public static int cnt;

    public static int[][] sum = new int[MAXN][MAXK];

    public static int[][] dp = new int[MAXN][MAXK];

    public static void build() {
        cnt = 1;
        Arrays.fill(head, 1, n + 1, 0);
    }

    public static void addEdge(int u, int v) {
        next[cnt] = head[u];
        to[cnt] = v;
        head[u] = cnt++;
    }

    // dfs1方法改迭代版
    // 不会改看讲解118，讲了怎么从递归版改成迭代版
    public static int[][] ufe = new int[MAXN][3];

    public static int stackSize;

    public static int u, f, e;

    public static void push(int u, int f, int e) {
        ufe[stackSize][0] = u;
        ufe[stackSize][1] = f;
        ufe[stackSize][2] = e;
        stackSize++;
    }

    public static void pop() {
        --stackSize;
        u = ufe[stackSize][0];
        f = ufe[stackSize][1];
        e = ufe[stackSize][2];
    }

    public static void dfs1(int root) {
        stackSize = 0;
        push(root, 0, -1);
        while (stackSize > 0) {
            pop();
            if (e == -1) {
                e = head[u];
            } else {
                e = next[e];
            }
            if (e != 0) {
                push(u, f, e);
                if (to[e] != f) {
                    push(to[e], u, -1);
                }
            } else {
                for (int e = head[u], v; e != 0; e = next[e]) {
                    v = to[e];
                    if (v != f) {
                        for (int j = 1; j <= k; j++) {
                            sum[u][j] += sum[v][j - 1];
                        }
                    }
                }
            }
        }
    }

    // dfs2方法改迭代版
    // 不会改看讲解118，讲了怎么从递归版改成迭代版
    public static void dfs2(int root) {
        stackSize = 0;
        push(root, 0, -1);
        while (stackSize > 0) {
            pop();
            if (e == -1) {
                e = head[u];
            } else {
                e = next[e];
            }
            if (e != 0) {
                push(u, f, e);
                int v = to[e];
                if (v != f) {
                    dp[v][0] = sum[v][0];
                    dp[v][1] = sum[v][1] + dp[u][0];
                    for (int i = 2; i <= k; i++) {
                        dp[v][i] = sum[v][i] + dp[u][i - 1] - sum[v][i - 2];
                    }
                    push(v, u, -1);
                }
            }
        }
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        n = (int) in.nval;
        build();
        in.nextToken();
        k = (int) in.nval;
        for (int i = 1, u, v; i < n; i++) {
            in.nextToken();
            u = (int) in.nval;
            in.nextToken();
            v = (int) in.nval;
            addEdge(u, v);
            addEdge(v, u);
        }
        for (int i = 1; i <= n; i++) {
            in.nextToken();
            sum[i][0] = (int) in.nval;
        }
        dfs1(1);
        for (int i = 0; i <= k; i++) {
            dp[1][i] = sum[1][i];
        }
        dfs2(1);
        for (int i = 1, ans; i <= n; i++) {
            ans = 0;
            for (int j = 0; j <= k; j++) {
                ans += dp[i][j];
            }
            out.println(ans);
        }
        out.flush();
        out.close();
        br.close();
    }

}
```

---

> ***last change: 2024/5/19***

---
