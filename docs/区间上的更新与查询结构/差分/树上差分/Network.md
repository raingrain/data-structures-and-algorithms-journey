# Network

## [Network](http://poj.org/problem?id=3417)

> - **Question**
>   - 有 `n` 个节点，给定 `n - 1` 条老边使其连接成一棵树，再给定 `m` 条新边额外加在树上。你可以切断两条边让这个图不联通，切断的两条边必须是一条老边和一条新边，返回使图不联通的方法数。

## Java

> - **边差分**

```java
import java.io.*;
import java.util.Arrays;

public class Main {

    public static int MAXN = 100001;

    public static int LIMIT = 17;

    public static int power;

    public static int log2(int n) {
        int ans = 0;
        while ((1 << ans) <= (n >> 1)) {
            ans++;
        }
        return ans;
    }

    public static int n, m;

    public static int[] num = new int[MAXN];

    public static int[] head = new int[MAXN];

    public static int[] next = new int[MAXN << 1];

    public static int[] to = new int[MAXN << 1];

    public static int cnt;

    public static int[] deep = new int[MAXN];

    public static int[][] stjump = new int[MAXN][LIMIT];

    public static int ans;

    public static void build() {
        power = log2(n);
        Arrays.fill(num, 1, n + 1, 0);
        cnt = 1;
        Arrays.fill(head, 1, n + 1, 0);
        ans = 0;
    }

    public static void addEdge(int u, int v) {
        next[cnt] = head[u];
        to[cnt] = v;
        head[u] = cnt++;
    }

    public static void dfs1(int u, int f) {
        deep[u] = deep[f] + 1;
        stjump[u][0] = f;
        for (int p = 1; p <= power; p++) {
            stjump[u][p] = stjump[stjump[u][p - 1]][p - 1];
        }
        for (int e = head[u]; e != 0; e = next[e]) {
            if (to[e] != f) {
                dfs1(to[e], u);
            }
        }
    }

    public static int lca(int a, int b) {
        if (deep[a] < deep[b]) {
            int tmp = a;
            a = b;
            b = tmp;
        }
        for (int p = power; p >= 0; p--) {
            if (deep[stjump[a][p]] >= deep[b]) {
                a = stjump[a][p];
            }
        }
        if (a == b) {
            return a;
        }
        for (int p = power; p >= 0; p--) {
            if (stjump[a][p] != stjump[b][p]) {
                a = stjump[a][p];
                b = stjump[b][p];
            }
        }
        return stjump[a][0];
    }

    public static void dfs2(int u, int f) {
        for (int e = head[u], v; e != 0; e = next[e]) {
            v = to[e];
            if (v != f) {
                dfs2(v, u);
            }
        }
        for (int e = head[u], v, w; e != 0; e = next[e]) {
            v = to[e];
            if (v != f) {
                w = 0 + num[v];
                if (w == 0) {
                    ans += m;
                } else if (w == 1) {
                    ans += 1;
                } else {
                    ans += 0;
                }
                num[u] += num[v];
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
        m = (int) in.nval;
        for (int i = 1, u, v; i < n; i++) {
            in.nextToken();
            u = (int) in.nval;
            in.nextToken();
            v = (int) in.nval;
            addEdge(u, v);
            addEdge(v, u);
        }
        dfs1(1, 0);
        for (int i = 1, u, v, lca; i <= m; i++) {
            in.nextToken();
            u = (int) in.nval;
            in.nextToken();
            v = (int) in.nval;
            lca = lca(u, v);
            num[u]++;
            num[v]++;
            num[lca] -= 2;
        }
        dfs2(1, 0);
        out.println(ans);
        out.flush();
        out.close();
        br.close();
    }

}
```
