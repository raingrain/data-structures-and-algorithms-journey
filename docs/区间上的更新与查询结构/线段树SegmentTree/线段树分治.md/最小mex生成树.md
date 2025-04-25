# 最小mex生成树

## [P5631 最小mex生成树](https://www.luogu.com.cn/problem/P5631)

> - **Question**
>   - 给定 `n` 个点， `m` 条边的无向连通图，边有边权，自然数集合 `S` 的 `mex` 含义为：最小的、没有出现在 `S` 中的自然数，现在你要求出一个这个图的生成树，使得其边权集合的 `mex` 尽可能小，对本题来说，注意 `0` 是自然数。
>   - **Tips**
>     - `1 <= n <= 10^6`
>     - `1 <= m <= 2 * 10^6`
>     - `0 <= 边权 <= 10^5`

## Java

> - **Question**

```java
// 答案从0开始考察，依次考察每个数值是否是答案，建立答案轴线段树
// 点x到点y的边，假设边权为w，该边的生效范围[0..w-1]、[w+1..最后]
// 来到线段树叶节点时，检查图是否全联通，第一个达标的叶节点就是答案

// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 1000001;
    public static int MAXV = 100001;
    public static int MAXT = 30000001;
    public static int n, m, v;

    public static int[] father = new int[MAXN];
    public static int[] siz = new int[MAXN];
    public static int[][] rollback = new int[MAXN][2];
    public static int opsize = 0;

    public static int[] head = new int[MAXV << 2];
    public static int[] next = new int[MAXT];
    public static int[] tox = new int[MAXT];
    public static int[] toy = new int[MAXT];
    public static int cnt = 0;

    public static int part;

    public static void addEdge(int i, int x, int y) {
        next[++cnt] = head[i];
        tox[cnt] = x;
        toy[cnt] = y;
        head[i] = cnt;
    }

    public static int find(int i) {
        while (i != father[i]) {
            i = father[i];
        }
        return i;
    }

    public static void union(int x, int y) {
        int fx = find(x);
        int fy = find(y);
        if (siz[fx] < siz[fy]) {
            int tmp = fx;
            fx = fy;
            fy = tmp;
        }
        father[fy] = fx;
        siz[fx] += siz[fy];
        rollback[++opsize][0] = fx;
        rollback[opsize][1] = fy;
    }

    public static void undo() {
        int fx = rollback[opsize][0];
        int fy = rollback[opsize--][1];
        father[fy] = fy;
        siz[fx] -= siz[fy];
    }

    public static void add(int jobl, int jobr, int jobx, int joby, int l, int r, int i) {
        if (jobl <= l && r <= jobr) {
            addEdge(i, jobx, joby);
        } else {
            int mid = (l + r) >> 1;
            if (jobl <= mid) {
                add(jobl, jobr, jobx, joby, l, mid, i << 1);
            }
            if (jobr > mid) {
                add(jobl, jobr, jobx, joby, mid + 1, r, i << 1 | 1);
            }
        }
    }

    public static int dfs(int l, int r, int i) {
        int unionCnt = 0;
        for (int ei = head[i], fx, fy; ei > 0; ei = next[ei]) {
            fx = find(tox[ei]);
            fy = find(toy[ei]);
            if (fx != fy) {
                union(fx, fy);
                part--;
                unionCnt++;
            }
        }
        int ans = -1;
        if (l == r) {
            if (part == 1) {
                ans = l;
            }
        } else {
            int mid = (l + r) >> 1;
            ans = dfs(l, mid, i << 1);
            if (ans == -1) {
                ans = dfs(mid + 1, r, i << 1 | 1);
            }
        }
        for (int k = 1; k <= unionCnt; k++) {
            undo();
            part++;
        }
        return ans;
    }

    public static void main(String[] args) {
        FastIO io = new FastIO(System.in, System.out);
        n = io.nextInt();
        m = io.nextInt();
        v = MAXV;
        for (int i = 1; i <= n; i++) {
            father[i] = i;
            siz[i] = 1;
        }
        for (int i = 1, x, y, w; i <= m; i++) {
            x = io.nextInt();
            y = io.nextInt();
            w = io.nextInt();
            if (w > 0) {
                add(0, w - 1, x, y, 0, v, 1);
            }
            add(w + 1, v, x, y, 0, v, 1);
        }
        part = n;
        io.writelnInt(dfs(0, v, 1));
        io.flush();
    }

    // 读写工具类
    static class FastIO {
        private final InputStream is;
        private final OutputStream os;
        private final byte[] inbuf = new byte[1 << 16];
        private int lenbuf = 0;
        private int ptrbuf = 0;
        private final StringBuilder outBuf = new StringBuilder();

        public FastIO(InputStream is, OutputStream os) {
            this.is = is;
            this.os = os;
        }

        private int readByte() {
            if (ptrbuf >= lenbuf) {
                ptrbuf = 0;
                try {
                    lenbuf = is.read(inbuf);
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
                if (lenbuf == -1) {
                    return -1;
                }
            }
            return inbuf[ptrbuf++] & 0xff;
        }

        private int skip() {
            int b;
            while ((b = readByte()) != -1) {
                if (b > ' ') {
                    return b;
                }
            }
            return -1;
        }

        public int nextInt() {
            int b = skip();
            if (b == -1) {
                throw new RuntimeException("No more integers (EOF)");
            }
            boolean negative = false;
            if (b == '-') {
                negative = true;
                b = readByte();
            }
            int val = 0;
            while (b >= '0' && b <= '9') {
                val = val * 10 + (b - '0');
                b = readByte();
            }
            return negative ? -val : val;
        }

        public void write(String s) {
            outBuf.append(s);
        }

        public void writeInt(int x) {
            outBuf.append(x);
        }

        public void writelnInt(int x) {
            outBuf.append(x).append('\n');
        }

        public void flush() {
            try {
                os.write(outBuf.toString().getBytes());
                os.flush();
                outBuf.setLength(0);
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        }
    }

}
```
