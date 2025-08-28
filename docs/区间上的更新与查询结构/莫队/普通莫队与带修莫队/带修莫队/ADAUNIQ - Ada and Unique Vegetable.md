# ADAUNIQ - Ada and Unique Vegetable

## [ADAUNIQ - Ada and Unique Vegetable](https://www.spoj.com/problems/ADAUNIQ/)

> - **Question**
>   - 给定一个长度为 `n` 的数组 `arr` ，下标 `0 ~ n-1` ，一共有 `m` 条操作，格式如下：
>     - 操作 `1 pos val` : 把 `arr[pos]` 的值设置成 `val` 。
>     - 操作 `2 l r`     : 查询 `arr[l ~ r]` 范围上，有多少种数出现了 `1` 次。
>   - **Tips**
>     - `0 <= n、m、arr[i] <= 2 * 10^5`

## Java

> - **带修莫队**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 200001;
    public static int n, m;
    public static int[] arr = new int[MAXN];
    public static int[] bi = new int[MAXN];

    public static int[][] query = new int[MAXN][4];
    public static int[][] update = new int[MAXN][2];
    public static int cntq, cntu;

    // 每种数字的词频统计
    public static int[] cnt = new int[MAXN];
    // curCnt代表出现次数1次的数有几种
    public static int curCnt = 0;

    public static int[] ans = new int[MAXN];

    public static class QueryCmp implements Comparator<int[]> {
        @Override
        public int compare(int[] a, int[] b) {
            if (bi[a[0]] != bi[b[0]]) {
                return bi[a[0]] - bi[b[0]];
            }
            if (bi[a[1]] != bi[b[1]]) {
                return bi[a[1]] - bi[b[1]];
            }
            return a[2] - b[2];
        }
    }

    public static void del(int num) {
        if (cnt[num] == 1) {
            curCnt--;
        }
        if (cnt[num] == 2) {
            curCnt++;
        }
        cnt[num]--;
    }

    public static void add(int num) {
        if (cnt[num] == 0) {
            curCnt++;
        }
        if (cnt[num] == 1) {
            curCnt--;
        }
        cnt[num]++;
    }

    public static void moveTime(int jobl, int jobr, int tim) {
        int pos = update[tim][0];
        int val = update[tim][1];
        if (jobl <= pos && pos <= jobr) {
            del(arr[pos]);
            add(val);
        }
        int tmp = arr[pos];
        arr[pos] = val;
        update[tim][1] = tmp;
    }

    public static void compute() {
        int winl = 1, winr = 0, wint = 0;
        for (int i = 1; i <= cntq; i++) {
            int jobl = query[i][0];
            int jobr = query[i][1];
            int jobt = query[i][2];
            int id = query[i][3];
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
            while (wint < jobt) {
                moveTime(jobl, jobr, ++wint);
            }
            while (wint > jobt) {
                moveTime(jobl, jobr, wint--);
            }
            ans[id] = curCnt;
        }
    }

    public static void prepare() {
        int blen = Math.max(1, (int) Math.pow(n, 2.0 / 3));
        for (int i = 1; i <= n; i++) {
            bi[i] = (i - 1) / blen + 1;
        }
        Arrays.sort(query, 1, cntq + 1, new QueryCmp());
    }

    public static void main(String[] args) throws Exception {
        FastReader in = new FastReader();
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        n = in.nextInt();
        m = in.nextInt();
        for (int i = 1; i <= n; i++) {
            arr[i] = in.nextInt();
        }
        for (int i = 1, op, l, r, pos, val; i <= m; i++) {
            op = in.nextInt();
            if (op == 1) {
                pos = in.nextInt();
                val = in.nextInt();
                cntu++;
                update[cntu][0] = pos + 1;
                update[cntu][1] = val;
            } else {
                l = in.nextInt();
                r = in.nextInt();
                cntq++;
                query[cntq][0] = l + 1;
                query[cntq][1] = r + 1;
                query[cntq][2] = cntu;
                query[cntq][3] = cntq;
            }
        }
        prepare();
        compute();
        for (int i = 1; i <= cntq; i++) {
            out.println(ans[i]);
        }
        out.flush();
        out.close();
    }

    // 读写工具类
    static class FastReader {
        final private int BUFFER_SIZE = 1 << 16;
        private final InputStream in;
        private final byte[] buffer;
        private int ptr, len;

        public FastReader() {
            in = System.in;
            buffer = new byte[BUFFER_SIZE];
            ptr = len = 0;
        }

        private boolean hasNextByte() throws IOException {
            if (ptr < len)
                return true;
            ptr = 0;
            len = in.read(buffer);
            return len > 0;
        }

        private byte readByte() throws IOException {
            if (!hasNextByte())
                return -1;
            return buffer[ptr++];
        }

        public char nextChar() throws IOException {
            byte c;
            do {
                c = readByte();
                if (c == -1)
                    return 0;
            } while (c <= ' ');
            char ans = 0;
            while (c > ' ') {
                ans = (char) c;
                c = readByte();
            }
            return ans;
        }

        public int nextInt() throws IOException {
            int num = 0;
            byte b = readByte();
            while (isWhitespace(b))
                b = readByte();
            boolean minus = false;
            if (b == '-') {
                minus = true;
                b = readByte();
            }
            while (!isWhitespace(b) && b != -1) {
                num = num * 10 + (b - '0');
                b = readByte();
            }
            return minus ? -num : num;
        }

        private boolean isWhitespace(byte b) {
            return b == ' ' || b == '\n' || b == '\r' || b == '\t';
        }
    }

}
```
