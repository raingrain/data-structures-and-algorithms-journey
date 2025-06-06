# MET-Meteors

## [P3527 [POI 2011] MET-Meteors](https://www.luogu.com.cn/problem/P3527)

> - **Question**
>   - 一共有 `n` 个国家，给定 `n` 个数字，表示每个国家希望收集到的陨石数量，一共有 `m` 个区域， `1` 号区顺时针到 `2` 号区， `m` 号区顺时针到 `1` 号区，即环形相连，每个区域只属于某一个国家，给定 `m` 个数字，表示每个区域归属给哪个国家，接下来会依次发生k场陨石雨，陨石雨格式 `l r num` ，含义如下：从 `l` 号区顺时针到 `r` 号区发生了陨石雨，每个区域都增加 `num` 个陨石，打印每个国家经历前几场陨石雨，可以达到收集要求，如果无法满足，打印 `NIE` 。
> - **Tips**
>   - `1 <= n、m、k <= 3 * 10^5`
>   - `1 <= 陨石数量 <= 10^9`

## Java

> - **方法一**

```java
// 破环成链 + 建立国家拥有区域的列表 + 整体二分 + 范围修改、单点查询的一维树状数组

// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 300001;
    public static int n, m, k;

    // 国家编号
    public static int[] qid = new int[MAXN];
    // 国家的需求
    public static int[] need = new int[MAXN];

    // 陨石雨的参数
    public static int[] rainl = new int[MAXN];
    public static int[] rainr = new int[MAXN];
    public static int[] num = new int[MAXN];

    // 国家拥有的区域列表
    public static int[] head = new int[MAXN];
    public static int[] next = new int[MAXN];
    public static int[] to = new int[MAXN];
    public static int cnt = 0;

    // 树状数组，支持范围修改、单点查询
    public static long[] tree = new long[MAXN << 1];

    // 整体二分
    public static int[] lset = new int[MAXN];
    public static int[] rset = new int[MAXN];

    // 每个国家的答案
    public static int[] ans = new int[MAXN];

    public static void addEdge(int i, int v) {
        next[++cnt] = head[i];
        to[cnt] = v;
        head[i] = cnt;
    }

    public static int lowbit(int i) {
        return i & -i;
    }

    public static void add(int i, int v) {
        int siz = m * 2;
        while (i <= siz) {
            tree[i] += v;
            i += lowbit(i);
        }
    }

    public static void add(int l, int r, int v) {
        add(l, v);
        add(r + 1, -v);
    }

    public static long query(int i) {
        long ret = 0;
        while (i > 0) {
            ret += tree[i];
            i -= lowbit(i);
        }
        return ret;
    }

    public static void compute(int ql, int qr, int vl, int vr) {
        if (ql > qr) {
            return;
        }
        if (vl == vr) {
            for (int i = ql; i <= qr; i++) {
                ans[qid[i]] = vl;
            }
        } else {
            int mid = (vl + vr) >> 1;
            for (int i = vl; i <= mid; i++) {
                add(rainl[i], rainr[i], num[i]);
            }
            int lsiz = 0, rsiz = 0;
            for (int i = ql; i <= qr; i++) {
                int id = qid[i];
                long satisfy = 0;
                for (int e = head[id]; e > 0; e = next[e]) {
                    satisfy += query(to[e]) + query(to[e] + m);
                    if (satisfy >= need[id]) {
                        break;
                    }
                }
                if (satisfy >= need[id]) {
                    lset[++lsiz] = id;
                } else {
                    need[id] -= satisfy;
                    rset[++rsiz] = id;
                }
            }
            for (int i = 1; i <= lsiz; i++) {
                qid[ql + i - 1] = lset[i];
            }
            for (int i = 1; i <= rsiz; i++) {
                qid[ql + lsiz + i - 1] = rset[i];
            }
            for (int i = vl; i <= mid; i++) {
                add(rainl[i], rainr[i], -num[i]);
            }
            compute(ql, ql + lsiz - 1, vl, mid);
            compute(ql + lsiz, qr, mid + 1, vr);
        }
    }

    public static void main(String[] args) throws Exception {
        FastReader in = new FastReader(System.in);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        n = in.nextInt();
        m = in.nextInt();
        for (int i = 1, nation; i <= m; i++) {
            nation = in.nextInt();
            addEdge(nation, i);
        }
        for (int i = 1; i <= n; i++) {
            qid[i] = i;
            need[i] = in.nextInt();
        }
        k = in.nextInt();
        for (int i = 1; i <= k; i++) {
            rainl[i] = in.nextInt();
            rainr[i] = in.nextInt();
            if (rainr[i] < rainl[i]) {
                rainr[i] += m;
            }
            num[i] = in.nextInt();
        }
        // 答案范围[1..k+1]，第k+1场陨石雨认为满足不了要求
        compute(1, n, 1, k + 1);
        for (int i = 1; i <= n; i++) {
            if (ans[i] == k + 1) {
                out.println("NIE");
            } else {
                out.println(ans[i]);
            }
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

> - **方法二**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 300001;
    public static int n, m, k;

    public static int[] qid = new int[MAXN];
    public static int[] need = new int[MAXN];

    public static int[] rainl = new int[MAXN];
    public static int[] rainr = new int[MAXN];
    public static int[] num = new int[MAXN];

    public static int[] head = new int[MAXN];
    public static int[] next = new int[MAXN];
    public static int[] to = new int[MAXN];
    public static int cnt = 0;

    public static long[] tree = new long[MAXN << 1];
    // 下了多少场陨石雨
    public static int used = 0;

    public static int[] lset = new int[MAXN];
    public static int[] rset = new int[MAXN];

    public static int[] ans = new int[MAXN];

    public static void addEdge(int i, int v) {
        next[++cnt] = head[i];
        to[cnt] = v;
        head[i] = cnt;
    }

    public static int lowbit(int i) {
        return i & -i;
    }

    public static void add(int i, int v) {
        int siz = m * 2;
        while (i <= siz) {
            tree[i] += v;
            i += lowbit(i);
        }
    }

    public static void add(int l, int r, int v) {
        add(l, v);
        add(r + 1, -v);
    }

    public static long query(int i) {
        long ret = 0;
        while (i > 0) {
            ret += tree[i];
            i -= lowbit(i);
        }
        return ret;
    }

    public static void compute(int ql, int qr, int vl, int vr) {
        if (ql > qr) {
            return;
        }
        if (vl == vr) {
            for (int i = ql; i <= qr; i++) {
                ans[qid[i]] = vl;
            }
        } else {
            int mid = (vl + vr) >> 1;
            int lsiz = 0, rsiz = 0;
            while (used < mid) {
                used++;
                add(rainl[used], rainr[used], num[used]);
            }
            while (used > mid) {
                add(rainl[used], rainr[used], -num[used]);
                used--;
            }
            for (int i = ql; i <= qr; i++) {
                int id = qid[i];
                long satisfy = 0;
                for (int e = head[id]; e > 0; e = next[e]) {
                    satisfy += query(to[e]) + query(to[e] + m);
                    if (satisfy >= need[id]) {
                        break;
                    }
                }
                if (satisfy >= need[id]) {
                    lset[++lsiz] = id;
                } else {
                    rset[++rsiz] = id;
                }
            }
            for (int i = 1; i <= lsiz; i++) {
                qid[ql + i - 1] = lset[i];
            }
            for (int i = 1; i <= rsiz; i++) {
                qid[ql + lsiz + i - 1] = rset[i];
            }
            compute(ql, ql + lsiz - 1, vl, mid);
            compute(ql + lsiz, qr, mid + 1, vr);
        }
    }

    public static void main(String[] args) throws Exception {
        FastReader in = new FastReader(System.in);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        n = in.nextInt();
        m = in.nextInt();
        for (int i = 1, nation; i <= m; i++) {
            nation = in.nextInt();
            addEdge(nation, i);
        }
        for (int i = 1; i <= n; i++) {
            qid[i] = i;
            need[i] = in.nextInt();
        }
        k = in.nextInt();
        for (int i = 1; i <= k; i++) {
            rainl[i] = in.nextInt();
            rainr[i] = in.nextInt();
            if (rainr[i] < rainl[i]) {
                rainr[i] += m;
            }
            num[i] = in.nextInt();
        }
        compute(1, n, 1, k + 1);
        for (int i = 1; i <= n; i++) {
            if (ans[i] == k + 1) {
                out.println("NIE");
            } else {
                out.println(ans[i]);
            }
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
