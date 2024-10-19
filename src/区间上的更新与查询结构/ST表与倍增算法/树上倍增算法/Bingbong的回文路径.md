# Bingbong的回文路径

## [Bingbong的回文路径](https://ac.nowcoder.com/acm/contest/78807/G)

> - ***Question***
>   - Bingbong有一棵有根树，根节点为 `1` ，总共有 `n` 个节点。树中的节点通过 `n - 1` 条无向边相连，每条边的权重为 `1` 。
>   - 树中的每个节点包含一个小写字母。Bingbong将选择从节点 `u` 开始，并在选择最短路径的情况下到达节点 `v` 。他想知道他所走路径形成的字符串是否是一个回文串。由于他会进行多次行走，您需要回答多个查询。
>   - ***输入描述***
>     - 第一行包含一个整数 `n (1 <= n <= 10^5)`，表示节点的数量。
>     - 第二行包含 `n` 个小写字母，其中第 `i` 个字母表示第 `i` 个节点上的小写字母。
>     - 第三行包含 `n` 个整数，其中第 `i` 个数字 `ai (1 <= ai <= i - 1)` 表示第 `i` 个节点的父节点，对于根节点 `ai = 0` 。
>     - 第四行包含一个整数 `q (1 <= q <= 10^5)` ，表示查询的数量。
>     - 接下来是 `q` 行，每行包含两个整数 `u` 和 `v` ，表示一个查询的起点和终点。
>   - ***输出描述***
>     - 输出 `q` 行，每行包含一个字符串。如果查询的起点到终点的路径连接形成一个回文串，则输出"YES"，否则输出"NO"。

---

## *Java*

> - ***树上倍增 + 字符串哈希***

```java
import java.io.*;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {

    public static int MAXN = 100001;

    public static int LIMIT = 17;

    public static int power;

    public static int[] s = new int[MAXN];

    public static int[] head = new int[MAXN];

    public static int[] to = new int[MAXN << 1];

    public static int[] next = new int[MAXN << 1];

    public static int cnt;

    public static int[] deep = new int[MAXN];

    public static int[][] jump = new int[MAXN][LIMIT];

    public static long K = 499;

    // kpow[i] = k的i次方
    public static long[] kpow = new long[MAXN];

    public static long[][] stup = new long[MAXN][LIMIT];

    public static long[][] stdown = new long[MAXN][LIMIT];

    public static void build(int n) {
        power = log2(n);
        cnt = 1;
        Arrays.fill(head, 1, n + 1, 0);
        kpow[0] = 1;
        for (int i = 1; i <= n; i++) {
            kpow[i] = kpow[i - 1] * K;
        }
    }

    public static int log2(int n) {
        int ans = 0;
        while ((1 << ans) <= (n >> 1)) {
            ans++;
        }
        return ans;
    }

    public static void addEdge(int u, int v) {
        next[cnt] = head[u];
        to[cnt] = v;
        head[u] = cnt++;
    }

    public static void dfs(int u, int f) {
        deep[u] = deep[f] + 1;
        jump[u][0] = f;
        stup[u][0] = stdown[u][0] = s[f];
        for (int p = 1, v; p <= power; p++) {
            v = jump[u][p - 1];
            jump[u][p] = jump[v][p - 1];
            stup[u][p] = stup[u][p - 1] * kpow[1 << (p - 1)] + stup[v][p - 1];
            stdown[u][p] = stdown[v][p - 1] * kpow[1 << (p - 1)] + stdown[u][p - 1];
        }
        for (int e = head[u], v; e != 0; e = next[e]) {
            v = to[e];
            if (v != f) {
                dfs(v, u);
            }
        }
    }

    public static boolean isPalindrome(int a, int b) {
        int lca = lca(a, b);
        long hash1 = hash(a, lca, b);
        long hash2 = hash(b, lca, a);
        return hash1 == hash2;
    }

    public static int lca(int a, int b) {
        if (deep[a] < deep[b]) {
            int tmp = a;
            a = b;
            b = tmp;
        }
        for (int p = power; p >= 0; p--) {
            if (deep[jump[a][p]] >= deep[b]) {
                a = jump[a][p];
            }
        }
        if (a == b) {
            return a;
        }
        for (int p = power; p >= 0; p--) {
            if (jump[a][p] != jump[b][p]) {
                a = jump[a][p];
                b = jump[b][p];
            }
        }
        return jump[a][0];
    }

    public static long hash(int from, int lca, int to) {
        // up是上坡hash值
        long up = s[from];
        for (int p = power; p >= 0; p--) {
            if (deep[jump[from][p]] >= deep[lca]) {
                up = up * kpow[1 << p] + stup[from][p];
                from = jump[from][p];
            }
        }
        if (to == lca) {
            return up;
        }
        // down是下坡hash值
        long down = s[to];
        // height是目前下坡的总高度
        int height = 1;
        for (int p = power; p >= 0; p--) {
            if (deep[jump[to][p]] > deep[lca]) {
                down = stdown[to][p] * kpow[height] + down;
                height += 1 << p;
                to = jump[to][p];
            }
        }
        return up * kpow[height] + down;
    }

    public static void main(String[] args) throws IOException {
        Kattio io = new Kattio();
        int n = io.nextInt();
        build(n);
        int si = 1;
        for (char c : io.next().toCharArray()) {
            s[si++] = c - 'a' + 1;
        }
        for (int u = 1, v; u <= n; u++) {
            v = io.nextInt();
            addEdge(u, v);
            addEdge(v, u);
        }
        dfs(1, 0);
        int m = io.nextInt();
        for (int i = 1, a, b; i <= m; i++) {
            a = io.nextInt();
            b = io.nextInt();
            io.println(isPalindrome(a, b) ? "YES" : "NO");
        }
        io.flush();
        io.close();
    }

    // 按行读字符串推荐用StringTokenizer
    // 参考链接 : https://oi-wiki.org/lang/java-pro/
    public static class Kattio extends PrintWriter {

        private BufferedReader r;
        private StringTokenizer st;

        public Kattio() {
            this(System.in, System.out);
        }

        public Kattio(InputStream i, OutputStream o) {
            super(o);
            r = new BufferedReader(new InputStreamReader(i));
        }

        public Kattio(String intput, String output) throws IOException {
            super(output);
            r = new BufferedReader(new FileReader(intput));
        }

        public String next() {
            try {
                while (st == null || !st.hasMoreTokens())
                    st = new StringTokenizer(r.readLine());
                return st.nextToken();
            } catch (Exception e) {
            }
            return null;
        }

        public int nextInt() {
            return Integer.parseInt(next());
        }

        public double nextDouble() {
            return Double.parseDouble(next());
        }

        public long nextLong() {
            return Long.parseLong(next());
        }

    }

}
```

---

> ***last change: 2024/5/19***

---
