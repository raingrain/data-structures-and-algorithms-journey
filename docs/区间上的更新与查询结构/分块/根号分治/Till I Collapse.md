# Till I Collapse

## [C. Till I Collapse](https://codeforces.com/problemset/problem/786/C)

> - **Question**
>   - 给定一个长度为 `n` 的数组 `arr` ，考虑如下问题的解，数组 `arr` 划分成若干段子数组，保证每段不同数字的种类 `<= k` ，返回至少划分成几段，打印 `k = 1, 2, 3, ..., n` 时，所有的答案。
>   - **Tips**
>     - `1 <= arr[i] <= n <= 10^5`

## Java

> - **Question**

```java
// 划分要求 * 划分数量 <= n，并且 划分要求 和 划分数量 之间存在单调性
// 时间复杂度O(n * √n * log n)

// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 100001;
    public static int n, blen;
    public static int[] arr = new int[MAXN];
    public static boolean[] vis = new boolean[MAXN];
    public static int[] ans = new int[MAXN];

    public static int query(int limit) {
        int kind = 0, cnt = 0, start = 1;
        for (int i = 1; i <= n; i++) {
            if (!vis[arr[i]]) {
                kind++;
                if (kind > limit) {
                    cnt++;
                    for (int j = start; j < i; j++) {
                        vis[arr[j]] = false;
                    }
                    start = i;
                    kind = 1;
                }
                vis[arr[i]] = true;
            }
        }
        if (kind > 0) {
            cnt++;
            for (int j = start; j <= n; j++) {
                vis[arr[j]] = false;
            }
        }
        return cnt;
    }

    public static int jump(int l, int r, int curAns) {
        int find = l;
        while (l <= r) {
            int mid = (l + r) >> 1;
            int check = query(mid);
            if (check < curAns) {
                r = mid - 1;
            } else if (check > curAns) {
                l = mid + 1;
            } else {
                find = mid;
                l = mid + 1;
            }
        }
        return find + 1;
    }

    public static void compute() {
        for (int i = 1; i <= blen; i++) {
            ans[i] = query(i);
        }
        for (int i = blen + 1; i <= n; i = jump(i, n, ans[i])) {
            ans[i] = query(i);
        }
    }

    public static void prepare() {
        int log2n = 0;
        while ((1 << log2n) <= (n >> 1)) {
            log2n++;
        }
        blen = Math.max(1, (int) Math.sqrt(n * log2n));
        Arrays.fill(ans, 1, n + 1, -1);
    }

    public static void main(String[] args) throws Exception {
        FastReader in = new FastReader(System.in);
        PrintWriter out = new PrintWriter(System.out);
        n = in.nextInt();
        for (int i = 1; i <= n; i++) {
            arr[i] = in.nextInt();
        }
        prepare();
        compute();
        for (int i = 1; i <= n; i++) {
            if (ans[i] == -1) {
                ans[i] = ans[i - 1];
            }
            out.print(ans[i] + " ");
        }
        out.println();
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
