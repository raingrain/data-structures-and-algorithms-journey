# Corn Fields G

## [P1879 [USACO06NOV] Corn Fields G](https://www.luogu.com.cn/problem/P1879)

> - ***Question***
>   - 给定一个 `n * m` 的二维网格 `grid` 。网格里只有 `0, 1` 两种值， `0` 表示该田地不能种草， `1` 表示该田地可以种草。种草的时候，任何两个种了草的田地不能相邻，相邻包括上、下、左、右四个方向。你可以随意决定种多少草，只要不破坏上面的规则即可。返回种草的方法数，答案对 `100000000` 取模。

## Java

> - ***状压DP***

```java
import java.io.*;

public class Main {

    public static int MAXN = 12;

    public static int MAXM = 12;

    public static int MOD = 100000000;

    public static int[][] grid = new int[MAXN][MAXM];

    public static int[][] dp = new int[MAXN][1 << MAXM];

    public static int n, m, maxs;

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        n = (int) in.nval;
        in.nextToken();
        m = (int) in.nval;
        maxs = 1 << m;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                in.nextToken();
                grid[i][j] = (int) in.nval;
            }
        }
        out.println(compute());
        out.flush();
        out.close();
        br.close();
    }

    // 时间复杂度O(n * 2的m次方 * 2的m次方)
    public static int compute() {
        for (int i = 0; i < n; i++) {
            for (int s = 0; s < maxs; s++) {
                dp[i][s] = -1;
            }
        }
        return f(0, 0);
    }

    public static int f(int i, int s) {
        if (i == n) {
            return 1;
        }
        if (dp[i][s] != -1) {
            return dp[i][s];
        }
        int ans = dfs(i, 0, s, 0);
        dp[i][s] = ans;
        return ans;
    }

    // 当前来到i行j列
    // i-1行每列种草的状况s
    // i行每列种草的状况ss
    // 返回后续有几种方法
    public static int dfs(int i, int j, int s, int ss) {
        if (j == m) {
            return f(i + 1, ss);
        }
        int ans = dfs(i, j + 1, s, ss);
        if (grid[i][j] == 1 && (j == 0 || get(ss, j - 1) == 0) && get(s, j) == 0) {
            ans = (ans + dfs(i, j + 1, s, set(ss, j, 1))) % MOD;
        }
        return ans;
    }

    // 得到状态s中j位的状态
    public static int get(int s, int j) {
        return (s >> j) & 1;
    }

    // 状态s中j位的状态设置成v，然后把新的值返回
    public static int set(int s, int j, int v) {
        return v == 0 ? (s & (~(1 << j))) : (s | (1 << j));
    }

}
```

> - ***轮廓线DP***

```java
import java.io.*;

public class Main {

    public static int MAXN = 12;

    public static int MAXM = 12;

    public static int MOD = 100000000;

    public static int[][] grid = new int[MAXN][MAXM];

    public static int[][][] dp = new int[MAXN][MAXM][1 << MAXM];

    public static int n, m, maxs;

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        n = (int) in.nval;
        in.nextToken();
        m = (int) in.nval;
        maxs = 1 << m;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                in.nextToken();
                grid[i][j] = (int) in.nval;
            }
        }
        out.println(compute());
        out.flush();
        out.close();
        br.close();
    }

    // 时间复杂度O(n * 2的m次方 * m)
    public static int compute() {
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                for (int s = 0; s < maxs; s++) {
                    dp[i][j][s] = -1;
                }
            }
        }
        return f(0, 0, 0);
    }

    // 当前来到i行j列
    // i-1行中，[j..m-1]列的种草状况用s[j..m-1]表示
    // i行中，[0..j-1]列的种草状况用s[0..j-1]表示
    // s表示轮廓线的状况
    // 返回后续有几种种草方法
    public static int f(int i, int j, int s) {
        if (i == n) {
            return 1;
        }
        if (j == m) {
            return f(i + 1, 0, s);
        }
        if (dp[i][j][s] != -1) {
            return dp[i][j][s];
        }
        int ans = f(i, j + 1, set(s, j, 0));
        if (grid[i][j] == 1 && (j == 0 || get(s, j - 1) == 0) && get(s, j) == 0) {
            ans = (ans + f(i, j + 1, set(s, j, 1))) % MOD;
        }
        dp[i][j][s] = ans;
        return ans;
    }

    public static int get(int s, int j) {
        return (s >> j) & 1;
    }

    public static int set(int s, int j, int v) {
        return v == 0 ? (s & (~(1 << j))) : (s | (1 << j));
    }

}
```

> - ***轮廓线DP + 空间压缩***

```java
import java.io.*;

public class Main {

    public static int MAXN = 12;

    public static int MAXM = 12;

    public static int MOD = 100000000;

    public static int[][] grid = new int[MAXN][MAXM];

    public static int[][] dp = new int[MAXM + 1][1 << MAXM];

    public static int[] prepare = new int[1 << MAXM];

    public static int n, m, maxs;

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        n = (int) in.nval;
        in.nextToken();
        m = (int) in.nval;
        maxs = 1 << m;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                in.nextToken();
                grid[i][j] = (int) in.nval;
            }
        }
        out.println(compute());
        out.flush();
        out.close();
        br.close();
    }

    public static int compute() {
        Arrays.fill(prepare, 0, maxs, 1);
        for (int i = n - 1; i >= 0; i--) {
            // j == m
            for (int s = 0; s < maxs; s++) {
                dp[m][s] = prepare[s];
            }
            // 普通位置
            for (int j = m - 1; j >= 0; j--) {
                for (int s = 0; s < maxs; s++) {
                    int ans = dp[j + 1][set(s, j, 0)];
                    if (grid[i][j] == 1 && (j == 0 || get(s, j - 1) == 0) && get(s, j) == 0) {
                        ans = (ans + dp[j + 1][set(s, j, 1)]) % MOD;
                    }
                    dp[j][s] = ans;
                }
            }
            // 设置prepare
            for (int s = 0; s < maxs; s++) {
                prepare[s] = dp[0][s];
            }
        }
        return dp[0][0];
    }

    public static int get(int s, int j) {
        return (s >> j) & 1;
    }

    public static int set(int s, int j, int v) {
        return v == 0 ? (s & (~(1 << j))) : (s | (1 << j));
    }

}
```
