# Removing Blocks

## [https://atcoder.jp/contests/agc028/tasks/agc028_b](https://atcoder.jp/contests/agc028/tasks/agc028_b)

> - **Question**
>   - 给定一个长度为 `n` 的数组 `arr` ， `arr[i]` 为 `i` 号砖块的重量，选择一个没有消除的砖块进行消除，收益为被消除砖块联通区域的重量之和，比如 `arr = {3, 5, 2, 1}` ，如果先消除 `5` ，那么获得 `3 + 5 + 2 + 1` 的收益， `arr = {3, X, 2, 1}` ，如果再消除 `1` ，那么获得 `2 + 1` 的收益， `arr = {3, X, 2, X}` ，如果再消除 `2` ，那么获得 `2` 的收益， `arr = {3, X, X, X}` ，如果再消除 `3` ，那么获得 `3` 的收益， `arr = {X, X, X, X}` ，一共有 `n!` 种消除方案，返回所有消除方案的收益总和，答案对 `1000000007` 取模。
>   - **Tips**
>     - `1 <= n <= 10^5`
>     - `1 <= arr[i] <= 10^9`

## Java

> - **笛卡尔树**

```java
// 消除方案的收益总和 -> i位置有多少祖先节点的期望 -> 利用笛卡尔树分析，x位置是i位置的祖先的概率

import java.io.*;
import java.util.*;

public class Main {

    public static int MOD = 1000000007;

    public static int MAXN = 100001;

    // 所有数字
    public static int[] arr = new int[MAXN];

    // 连续数字逆元表
    public static int[] inv = new int[MAXN];

    // sum[i] = (1/1 + 1/2 + 1/3 + ... + 1/i)，%MOD意义下的余数
    public static int[] sum = new int[MAXN];

    public static int n;

    public static void build() {
        inv[1] = 1;
        for (int i = 2; i <= n; i++) {
            inv[i] = (int) (MOD - (long) inv[MOD % i] * (MOD / i) % MOD);
        }
        for (int i = 1; i <= n; i++) {
            sum[i] = (sum[i - 1] + inv[i]) % MOD;
        }
    }

    public static long compute() {
        build();
        long ans = 0;
        for (int i = 1; i <= n; i++) {
            ans = (ans + (long) (sum[i] + sum[n - i + 1] - 1) * arr[i]) % MOD;
        }
        for (int i = 1; i <= n; ++i) {
            ans = ans * i % MOD;
        }
        return ans;
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        n = (int) in.nval;
        for (int i = 1; i <= n; i++) {
            in.nextToken();
            arr[i] = (int) in.nval;
        }
        out.println(compute());
        out.flush();
        out.close();
        br.close();
    }

}
```
