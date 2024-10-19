# Buying Hay S

## [P2918 [USACO08NOV] Buying Hay S](https://www.luogu.com.cn/problem/P2918)

> - ***Question***
>   - 约翰的干草库存已经告罄，他打算为奶牛们采购 `H (1 <= H <= 50000)` 磅干草。
>   - 他知道 `N (1 <= N <= 100)` 个干草公司，现在用 `1` 到 `N` 给它们编号。第 `i` 公司卖的干草包重量为 `Pi (1 <= Pi <= 5000)` 磅，需要的开销为 `Ci (1 <= Ci <= 5000)` 美元。每个干草公司的货源都十分充足，可以卖出无限多的干草包。
>   - 帮助约翰找到最小的开销来满足需要，即采购到至少 `H` 磅干草。
>   - ***输入描述***
>     - Line 1: Two space-separated integers: N and H.
>     - Lines 2..N+1: Line i+1 contains two space-separated integers: P_i and C_i.
>   - ***输出描述***
>     - Line 1: A single integer representing the minimum cost FJ needs to pay to obtain at least H pounds of hay.
>   - ***tips:***
>     - FJ can buy three packages from the second supplier for a total cost of 9.

## Java

> - ***动态规划***

```java
import java.io.*;
import java.util.Arrays;

public class Main {

    public static int MAXN = 101;

    public static int MAXM = 55001;

    public static int[] val = new int[MAXN];

    public static int[] cost = new int[MAXN];

    public static int[] dp = new int[MAXM];

    public static int n, h, maxv, m;

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            n = (int) in.nval;
            in.nextToken();
            h = (int) in.nval;
            maxv = 0;
            for (int i = 1; i <= n; i++) {
                in.nextToken();
                val[i] = (int) in.nval;
                maxv = Math.max(maxv, val[i]);
                in.nextToken();
                cost[i] = (int) in.nval;
            }
            // 最核心的一句
            // 包含重要分析
            m = h + maxv;
            out.println(compute2());
        }
        out.flush();
        out.close();
        br.close();
    }

    // dp[i][j] : 1...i里挑公司，购买严格j磅干草，需要的最少花费
    // 1) dp[i-1][j]
    // 2) dp[i][j-val[i]] + cost[i]
    // 两种可能性中选最小
    // 但是关于j需要进行一定的扩充，原因视频里讲了
    // 扩充为要买的加上最大规格
    public static int compute1() {
        int[][] dp = new int[n + 1][m + 1];
        Arrays.fill(dp[0], 1, m + 1, Integer.MAX_VALUE);
        for (int i = 1; i <= n; i++) {
            for (int j = 0; j <= m; j++) {
                dp[i][j] = dp[i - 1][j];
                if (j - val[i] >= 0 && dp[i][j - val[i]] != Integer.MAX_VALUE) {
                    dp[i][j] = Math.min(dp[i][j], dp[i][j - val[i]] + cost[i]);
                }
            }
        }
        int ans = Integer.MAX_VALUE;
        // >= h
        // h h+1 h+2 ... m
        for (int j = h; j <= m; j++) {
            ans = Math.min(ans, dp[n][j]);
        }
        return ans;
    }

    // 空间压缩
    public static int compute2() {
        Arrays.fill(dp, 1, m + 1, Integer.MAX_VALUE);
        for (int i = 1; i <= n; i++) {
            for (int j = val[i]; j <= m; j++) {
                if (dp[j - val[i]] != Integer.MAX_VALUE) {
                    dp[j] = Math.min(dp[j], dp[j - val[i]] + cost[i]);
                }
            }
        }
        int ans = Integer.MAX_VALUE;
        for (int j = h; j <= m; j++) {
            ans = Math.min(ans, dp[j]);
        }
        return ans;
    }

}
```
