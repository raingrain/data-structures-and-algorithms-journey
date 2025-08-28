# 小 Z 的袜子

## [P1494 [国家集训队] 小 Z 的袜子](https://www.luogu.com.cn/problem/P1494)

> - **Question**
>   - 给定一个长度为 `n` 的数组 `arr` ，一共有 `m` 条查询，格式如下：查询 `l r` 表示 `arr[l ~ r]`范围上，随机选不同位置的两个数，打印数值相同的概率，概率用分数的形式表达，并且约分到最简的形式。
>   - **Tips**
>     - `1 <= n、m、arr[i] <= 5 * 10^4`

## Java

> - **普通莫队**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 50001;
    public static int n, m;
    public static int[] arr = new int[MAXN];
    public static int[][] query = new int[MAXN][3];

    public static int[] bi = new int[MAXN];
    public static int[] cnt = new int[MAXN];
    public static long sum = 0;

    // 每个查询的分子的值
    public static long[] ans1 = new long[MAXN];
    // 每个查询的分母的值
    public static long[] ans2 = new long[MAXN];

    public static class QueryCmp implements Comparator<int[]> {

        @Override
        public int compare(int[] a, int[] b) {
            if (bi[a[0]] != bi[b[0]]) {
                return bi[a[0]] - bi[b[0]];
            }
            return a[1] - b[1];
        }

    }

    public static long gcd(long a, long b) {
        return b == 0 ? a : gcd(b, a % b);
    }

    public static void del(int num) {
        sum -= cnt[num] * cnt[num];
        cnt[num]--;
        sum += cnt[num] * cnt[num];
    }

    public static void add(int num) {
        sum -= cnt[num] * cnt[num];
        cnt[num]++;
        sum += cnt[num] * cnt[num];
    }

    public static void prepare() {
        int blen = (int) Math.sqrt(n);
        for (int i = 1; i <= n; i++) {
            bi[i] = (i - 1) / blen + 1;
        }
        Arrays.sort(query, 1, m + 1, new QueryCmp());
    }

    public static void compute() {
        int winl = 1, winr = 0;
        for (int i = 1; i <= m; i++) {
            int jobl = query[i][0];
            int jobr = query[i][1];
            int id = query[i][2];
            while (winl > jobl) {
                add(arr[--winl]);
            }
            while (winr < jobr) {
                add(arr[++winr]);
            }
            while (winl < jobl) {
                del(arr[winl++]);
            }
            while (winr > jobr) {
                del(arr[winr--]);
            }
            if (jobl == jobr) {
                ans1[id] = 0;
                ans2[id] = 1;
            } else {
                ans1[id] = sum - (jobr - jobl + 1);
                ans2[id] = 1L * (jobr - jobl + 1) * (jobr - jobl);
                long g = gcd(ans1[id], ans2[id]);
                ans1[id] /= g;
                ans2[id] /= g;
            }
        }
    }

    public static void main(String[] args) throws Exception {
        FastReader in = new FastReader(System.in);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        n = in.nextInt();
        m = in.nextInt();
        for (int i = 1; i <= n; i++) {
            arr[i] = in.nextInt();
        }
        for (int i = 1; i <= m; i++) {
            query[i][0] = in.nextInt();
            query[i][1] = in.nextInt();
            query[i][2] = i;
        }
        prepare();
        compute();
        for (int i = 1; i <= m; i++) {
            out.println(ans1[i] + "/" + ans2[i]);
        }
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
