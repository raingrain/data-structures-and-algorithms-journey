# Give Away

## [GIVEAWAY - Give Away](https://www.spoj.com/problems/GIVEAWAY/)

> - **Question**
>   - 给定一个长度为 `n` 的数组 `arr` ，接下来有 `m` 条操作，每条操作是如下两种类型中的一种：
>     - 操作 `0 a b c` ：打印 `arr[a ~ b]` 范围上 `>=c` 的数字个数。
>     - 操作 `1 a b` ：把 `arr[a]` 的值改成 `b` 。
>   - **Tips**
>     - `1 <= n <= 5 * 10^5`
>     - `1 <= m <= 10^5`
>     - `1 <= 数组中的值 <= 10^9`

## Java

> - **Question**

```java
// 时间复杂度O(m * √n * log √n)，相当极限，但是分块常数小，可以通过

// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 500001;
    public static int MAXB = 1001;
    public static int n, m;
    public static int[] arr = new int[MAXN];
    public static int[] sortv = new int[MAXN];

    public static int blen, bnum;
    public static int[] bi = new int[MAXN];
    public static int[] bl = new int[MAXB];
    public static int[] br = new int[MAXB];

    public static void build() {
        blen = (int) Math.sqrt(n);
        bnum = (n + blen - 1) / blen;
        for (int i = 1; i <= n; i++) {
            bi[i] = (i - 1) / blen + 1;
        }
        for (int i = 1; i <= bnum; i++) {
            bl[i] = (i - 1) * blen + 1;
            br[i] = Math.min(i * blen, n);
        }
        for (int i = 1; i <= n; i++) {
            sortv[i] = arr[i];
        }
        for (int i = 1; i <= bnum; i++) {
            Arrays.sort(sortv, bl[i], br[i] + 1);
        }
    }

    public static int getCnt(int i, int v) {
        int l = bl[i], r = br[i], m, ans = 0;
        while (l <= r) {
            m = (l + r) >> 1;
            if (sortv[m] >= v) {
                ans += r - m + 1;
                r = m - 1;
            } else {
                l = m + 1;
            }
        }
        return ans;
    }

    public static int query(int l, int r, int v) {
        int ans = 0;
        if (bi[l] == bi[r]) {
            for (int i = l; i <= r; i++) {
                if (arr[i] >= v) {
                    ans++;
                }
            }
        } else {
            for (int i = l; i <= br[bi[l]]; i++) {
                if (arr[i] >= v) {
                    ans++;
                }
            }
            for (int i = bl[bi[r]]; i <= r; i++) {
                if (arr[i] >= v) {
                    ans++;
                }
            }
            for (int i = bi[l] + 1; i <= bi[r] - 1; i++) {
                ans += getCnt(i, v);
            }
        }
        return ans;
    }

    public static void update(int i, int v) {
        int l = bl[bi[i]];
        int r = br[bi[i]];
        arr[i] = v;
        for (int j = l; j <= r; j++) {
            sortv[j] = arr[j];
        }
        Arrays.sort(sortv, l, r + 1);
    }

    public static void main(String[] args) throws IOException {
        FastReader in = new FastReader(System.in);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        n = in.nextInt();
        for (int i = 1; i <= n; i++) {
            arr[i] = in.nextInt();
        }
        build();
        m = in.nextInt();
        for (int i = 1, op, a, b, c; i <= m; i++) {
            op = in.nextInt();
            a = in.nextInt();
            b = in.nextInt();
            if (op == 0) {
                c = in.nextInt();
                out.println(query(a, b, c));
            } else {
                update(a, b);
            }
        }
        out.flush();
        out.close();
    }

    // 读写工具类
    static class FastReader {
        private final byte[] buffer = new byte[1 << 20];
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
