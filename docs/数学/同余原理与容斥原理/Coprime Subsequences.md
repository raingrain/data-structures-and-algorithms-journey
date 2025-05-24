# Coprime Subsequences

## [Coprime Subsequences](https://codeforces.com/problemset/problem/803/F)

> - **Question**
>   - 给你一个序列，问你有多少个子序列的 `gcd = 1` 。

## Java

> - **容斥原理**

```java
import java.io.*;

public class Main {
    
    public static int MOD = 1000000007;

    public static int LIMIT = 100000;

    public static long[] dp = new long[LIMIT + 1];

    public static long[] cnt = new long[LIMIT + 1];

    public static long[] pow2 = new long[LIMIT + 1];

    public static void build() {
        pow2[0] = 1;
        for (int i = 1; i <= LIMIT; i++) {
            pow2[i] = (pow2[i - 1] * 2) % MOD;
        }
    }

    public static void main(String[] args) throws IOException {
        build();
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            int n = (int) in.nval;
            for (int i = 1; i <= n; i++) {
                in.nextToken();
                cnt[(int) in.nval]++;
            }
            out.println(compute());
        }
        out.flush();
        out.close();
        br.close();
    }

    // 时间复杂度O(n * logn)
    public static long compute() {
        for (int i = LIMIT; i >= 1; i--) {
            long counts = 0;
            // cnt[j] 为j 2j 3j 4j 5j 6j的个数有几个
            for (int j = i; j <= LIMIT; j += i) {
                counts = (counts + cnt[j]) % MOD;
            }
            dp[i] = (pow2[(int) counts] - 1 + MOD) % MOD;
            for (int j = 2 * i; j <= LIMIT; j += i) {
                // dp[i] 表示以i为gcd的子序列有几个
                // 用以i 2i 3i 4i 5i 6i为gcd（或）的子序列个数减去dp[2i] dp[3i] dp[4i] dp[5i] dp[6i]即可
                dp[i] = (dp[i] - dp[j] + MOD) % MOD;
            }
        }
        return dp[1];
    }

}
```
