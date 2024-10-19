# Mondriaan's Dream

## [Mondriaan's Dream](http://poj.org/problem?id=2411)

> - ***Question***
>   - 给定两个参数 `n` 和 `m` ，表示 `n` 行 `m` 列的空白区域，有无限多的 `1 * 2` 规格的瓷砖，目标是严丝合缝的铺满所有的空白区域，返回有多少种铺满的方法。

---

## *Java*

> - ***轮廓线DP***

```java
import java.io.*;

public class Main {

    public static int MAXN = 11;

    public static long[][][] dp = new long[MAXN][MAXN][1 << MAXN];

    public static int n, m, maxs;

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            n = (int) in.nval;
            in.nextToken();
            m = (int) in.nval;
            maxs = 1 << m;
            if (n != 0 || m != 0) {
                out.println(compute());
            }
        }
        out.flush();
        out.close();
        br.close();
    }

    public static long compute() {
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
    // i-1行中，[j..m-1]列有没有作为竖砖的上点，状况用s[j..m-1]表示
    // i行中，[0..j-1]列有没有作为竖砖的上点，状况用s[0..j-1]表示
    // s表示轮廓线的状况
    // 返回后续有几种摆放的方法
    public static long f(int i, int j, int s) {
        if (i == n) {
            return 1;
        }
        if (j == m) {
            return f(i + 1, 0, s);
        }
        if (dp[i][j][s] != -1) {
            return dp[i][j][s];
        }
        long ans = 0;
        if (get(s, j) == 1) {
            ans = f(i, j + 1, set(s, j, 0));
        } else { // 上方没有竖着摆砖
            if (i + 1 < n) { // 当前竖着摆砖
                ans = f(i, j + 1, set(s, j, 1));
            }
            if (j + 1 < m && get(s, j + 1) == 0) { // 当前横着摆砖
                ans += f(i, j + 2, s);
            }
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
import java.util.Arrays;

public class Main {

    public static int MAXN = 11;

    public static long[][] dp = new long[MAXN + 1][1 << MAXN];

    public static long[] prepare = new long[1 << MAXN];

    public static int n, m, maxs;

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            n = (int) in.nval;
            in.nextToken();
            m = (int) in.nval;
            maxs = 1 << m;
            if (n != 0 || m != 0) {
                out.println(compute());
            }
        }
        out.flush();
        out.close();
        br.close();
    }

    public static long compute() {
        Arrays.fill(prepare, 0, maxs, 1);
        for (int i = n - 1; i >= 0; i--) {
            // j == m
            for (int s = 0; s < maxs; s++) {
                dp[m][s] = prepare[s];
            }
            // 普通位置
            for (int j = m - 1; j >= 0; j--) {
                for (int s = 0; s < maxs; s++) {
                    long ans = 0;
                    if (get(s, j) == 1) {
                        ans = dp[j + 1][set(s, j, 0)];
                    } else {
                        if (i + 1 < n) {
                            ans = dp[j + 1][set(s, j, 1)];
                        }
                        if (j + 1 < m && get(s, j + 1) == 0) {
                            ans += dp[j + 2][s];
                        }
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
