# Dynamic Rankings

## [Dynamic Rankings](https://www.luogu.com.cn/problem/P2617)

> - **Question**
>   - 给定一个长度为 `n` 的数组 `arr` ，下标 `1 ~ n` ，每条操作都是如下 `2` 种类型中的一种，一共进行 `m` 次操作：
>     - 操作 `Q x y z` ：查询 `arr[x ~ y]` 中排第 `z` 名的数字。
>     - 操作 `C x y` ： `arr` 中 `x` 位置的数字改成 `y` 。
>   - **Tips**
>     - `1 <= n、m <= 10^5`
>     - 数组中的值永远在 `[0, 10^9]` 范围内

## Java

> - **修改和查询装在一起的整体二分 + 树状数组**

```java
// 树套树模版题，现在作为带修改的整体二分模版题

// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 100001;
    public static int MAXE = MAXN << 2;
    public static int INF = 1000000001;
    public static int n, m;

    public static int[] arr = new int[MAXN];

    // 事件编号组成的数组
    public static int[] eid = new int[MAXE];
    // op == 1，代表修改事件，x处，值y，效果v
    // op == 2，代表查询事件，[x..y]范围上查询第v小，q表示问题的编号
    public static int[] op = new int[MAXE];
    public static int[] x = new int[MAXE];
    public static int[] y = new int[MAXE];
    public static int[] v = new int[MAXE];
    public static int[] q = new int[MAXE];
    public static int cnte = 0;
    public static int cntq = 0;

    // 树状数组
    public static int[] tree = new int[MAXN];

    // 整体二分
    public static int[] lset = new int[MAXE];
    public static int[] rset = new int[MAXE];

    // 查询的答案
    public static int[] ans = new int[MAXN];

    public static int lowbit(int i) {
        return i & -i;
    }

    public static void add(int i, int v) {
        while (i <= n) {
            tree[i] += v;
            i += lowbit(i);
        }
    }

    public static int sum(int i) {
        int ret = 0;
        while (i > 0) {
            ret += tree[i];
            i -= lowbit(i);
        }
        return ret;
    }

    public static int query(int l, int r) {
        return sum(r) - sum(l - 1);
    }

    public static void compute(int el, int er, int vl, int vr) {
        if (el > er) {
            return;
        }
        if (vl == vr) {
            for (int i = el; i <= er; i++) {
                int id = eid[i];
                if (op[id] == 2) {
                    ans[q[id]] = vl;
                }
            }
        } else {
            int mid = (vl + vr) >> 1;
            int lsiz = 0, rsiz = 0;
            for (int i = el; i <= er; i++) {
                int id = eid[i];
                if (op[id] == 1) {
                    if (y[id] <= mid) {
                        add(x[id], v[id]);
                        lset[++lsiz] = id;
                    } else {
                        rset[++rsiz] = id;
                    }
                } else {
                    int satisfy = query(x[id], y[id]);
                    if (v[id] <= satisfy) {
                        lset[++lsiz] = id;
                    } else {
                        v[id] -= satisfy;
                        rset[++rsiz] = id;
                    }
                }
            }
            for (int i = 1; i <= lsiz; i++) {
                eid[el + i - 1] = lset[i];
            }
            for (int i = 1; i <= rsiz; i++) {
                eid[el + lsiz + i - 1] = rset[i];
            }
            for (int i = 1; i <= lsiz; i++) {
                int id = lset[i];
                if (op[id] == 1 && y[id] <= mid) {
                    add(x[id], -v[id]);
                }
            }
            compute(el, el + lsiz - 1, vl, mid);
            compute(el + lsiz, er, mid + 1, vr);
        }
    }

    public static void main(String[] args) throws IOException {
        FastReader in = new FastReader();
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        n = in.nextInt();
        m = in.nextInt();
        for (int i = 1; i <= n; i++) {
            arr[i] = in.nextInt();
            op[++cnte] = 1;
            x[cnte] = i;
            y[cnte] = arr[i];
            v[cnte] = 1;
        }
        char type;
        for (int i = 1; i <= m; i++) {
            type = in.nextChar();
            if (type == 'C') {
                int a = in.nextInt();
                int b = in.nextInt();
                op[++cnte] = 1;
                x[cnte] = a;
                y[cnte] = arr[a];
                v[cnte] = -1;
                op[++cnte] = 1;
                x[cnte] = a;
                y[cnte] = b;
                v[cnte] = 1;
                arr[a] = b;
            } else {
                op[++cnte] = 2;
                x[cnte] = in.nextInt();
                y[cnte] = in.nextInt();
                v[cnte] = in.nextInt();
                q[cnte] = ++cntq;
            }
        }
        for (int i = 1; i <= cnte; i++) {
            eid[i] = i;
        }
        compute(1, cnte, 0, INF);
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
