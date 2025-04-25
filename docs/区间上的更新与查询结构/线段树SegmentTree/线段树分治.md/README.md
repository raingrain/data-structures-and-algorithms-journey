# 线段树分支分治

## 线段树分治的原理

1. 一些问题中，可以把每条操作分配一个时间点。
2. 建立时间轴线段树，每个时间点都是线段树的下标。
3. 每种修改操作，拥有若干个有效时间段，对应着线段树上的若干区间。
4. 每条查询操作，其实就是询问，在某个时间点上，相关的状态信息。

## 线段树分治的过程

1. 先把所有操作记录下来，然后把操作分类，每种修改操作，整理出若干个有效时间段。
2. 根据每个有效时间段，把任务分配到线段树的区间上。
3. 遍历整棵线段树，来到某个线段树的区间时，执行相应的任务，离开区间时，撤销相应的任务。
4. 线段树的叶节点，对应着单个时间点，如果有查询操作，记录查询的答案。

## 线段树分治的使用场景

1. 在线算法实现困难较大，可能需要更高级的数据结构，或者时间复杂度无法达到预期。
2. 题目没有强制在线的要求，并且操作的次序可以转化成离线的时序。
3. 某些问题，使用在线算法，也许能得到很好的解决，但一般难度会偏大。
4. 线段树分治提供了一种离线解决问题的思路。

## 线段树分治的时间复杂度

1. 操作的数量为 `m` ，每个有效时间段，会分解成 `logm` 个线段树的区间。
2. 每个线段树区间都留有这个操作的任务，那么任务总数为 `m * logm` 。
3. 执行任务和撤销任务，使用到可撤销并查集，并查集上点的个数为 `n` ，单次操作的代价为 `logn` 。
4. 所以时间复杂度一般为 `O(m * logm * logn)` 。

## [#121. 「离线可过」动态图连通性](https://loj.ac/p/121)

> - **Question 1**
>   - 一共有 `n` 个节点，一共有 `m` 条操作，每条操作是如下三种类型中的一种：
>     - 操作 `0 x y` ：点 `x` 和点 `y` 之间一定没有边，现在增加一条边。
>     - 操作 `1 x y` ：点 `x` 和点 `y` 之间一定存在边，现在删除这条边。
>     - 操作 `2 x y` ：查询点 `x` 和点 `y` 是否联通。
>   - **Tips**
>     - `1 <= n <= 5000`
>     - `1 <= m <= 500000`

## [P5787 二分图 /【模板】线段树分治](https://www.luogu.com.cn/problem/P5787)

> - **Question 2**
>   - 一共有 `n` 个节点，时刻的范围 `0 ~ k` ，一共有 `m` 条操作，每条操作含义如下：操作 `x y l r` ：点 `x` 到点 `y` 之间连一条边，该边在 `l` 时刻出现，在 `r` 时刻消失。分别打印 `1时刻以内, 2时刻以内, ..., k时刻以内` ，图是不是二分图，注意 `i` 时刻以内是 `0 ~ i - 1` 时间段的意思。
>   - **Tips**
>     - `1 <= n、k <= 10^5`
>     - `1 <= m <= 2 * 10^5`
>     - `1 <= x、y <= n`
>     - `0 <= l、r <= k`

## Java

> - **Question 1**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    // 点的数量最大值
    public static int MAXN = 5001;
    // 操作数量最大值
    public static int MAXM = 500001;
    // 任务数量最大值
    public static int MAXT = 5000001;

    public static int n, m;

    // 操作类型op、端点u、端点v
    public static int[] op = new int[MAXM];
    public static int[] u = new int[MAXM];
    public static int[] v = new int[MAXM];

    // last[x][y] : 点x和点y的边，上次出现的时间点
    public static int[][] last = new int[MAXN][MAXN];

    // 可撤销并查集
    public static int[] father = new int[MAXN];
    public static int[] siz = new int[MAXN];
    public static int[][] rollback = new int[MAXN][2];
    public static int opsize = 0;

    // 线段树每个区间拥有哪些任务的列表，链式前向星表示
    public static int[] head = new int[MAXM << 2];
    public static int[] next = new int[MAXT];
    public static int[] tox = new int[MAXT];
    public static int[] toy = new int[MAXT];
    public static int cnt = 0;

    // ans[i]为第i条操作的答案，只有查询操作才有答案
    public static boolean[] ans = new boolean[MAXM];

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

    public static void dfs(int l, int r, int i) {
        int unionCnt = 0;
        for (int ei = head[i], x, y, fx, fy; ei > 0; ei = next[ei]) {
            x = tox[ei];
            y = toy[ei];
            fx = find(x);
            fy = find(y);
            if (fx != fy) {
                union(fx, fy);
                unionCnt++;
            }
        }
        if (l == r) {
            if (op[l] == 2) {
                ans[l] = find(u[l]) == find(v[l]);
            }
        } else {
            int mid = (l + r) / 2;
            dfs(l, mid, i << 1);
            dfs(mid + 1, r, i << 1 | 1);
        }
        for (int j = 1; j <= unionCnt; j++) {
            undo();
        }
    }

    public static void prepare() {
        for (int i = 1; i <= n; i++) {
            father[i] = i;
            siz[i] = 1;
        }
        for (int i = 1, t, x, y; i <= m; i++) {
            t = op[i];
            x = u[i];
            y = v[i];
            if (t == 0) {
                last[x][y] = i;
            } else if (t == 1) {
                add(last[x][y], i - 1, x, y, 1, m, 1);
                last[x][y] = 0;
            }
        }
        for (int x = 1; x <= n; x++) {
            for (int y = x + 1; y <= n; y++) {
                if (last[x][y] != 0) {
                    add(last[x][y], m, x, y, 1, m, 1);
                }
            }
        }
    }

    public static void main(String[] args) {
        FastIO io = new FastIO(System.in, System.out);
        n = io.nextInt();
        m = io.nextInt();
        for (int i = 1, t, x, y; i <= m; i++) {
            t = io.nextInt();
            x = io.nextInt();
            y = io.nextInt();
            op[i] = t;
            u[i] = Math.min(x, y);
            v[i] = Math.max(x, y);
        }
        prepare();
        dfs(1, m, 1);
        for (int i = 1; i <= m; i++) {
            if (op[i] == 2) {
                if (ans[i]) {
                    io.write("Y\n");
                } else {
                    io.write("N\n");
                }
            }
        }
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

> - **Question 2**

```java
// For Most Online Judge systems

// 因为每次判断，i时刻以内是不是二分图，所以点x到点y的边，让其生效时间为[l+1..r]

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 100001;
    public static int MAXT = 3000001;
    public static int n, m, k;

    public static int[] father = new int[MAXN << 1];
    public static int[] siz = new int[MAXN << 1];
    public static int[][] rollback = new int[MAXN << 1][2];
    public static int opsize = 0;

    public static int[] head = new int[MAXN << 2];
    public static int[] next = new int[MAXT];
    public static int[] tox = new int[MAXT];
    public static int[] toy = new int[MAXT];
    public static int cnt = 0;

    public static boolean[] ans = new boolean[MAXN];

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
            int mid = (l + r) / 2;
            if (jobl <= mid) {
                add(jobl, jobr, jobx, joby, l, mid, i << 1);
            }
            if (jobr > mid) {
                add(jobl, jobr, jobx, joby, mid + 1, r, i << 1 | 1);
            }
        }
    }

    public static void dfs(int l, int r, int i) {
        boolean check = true;
        int unionCnt = 0;
        for (int ei = head[i]; ei > 0; ei = next[ei]) {
            int x = tox[ei], y = toy[ei], fx = find(x), fy = find(y);
            if (fx == fy) {
                check = false;
                break;
            } else {
                union(x, y + n);
                union(y, x + n);
                unionCnt += 2;
            }
        }
        if (check) {
            if (l == r) {
                ans[l] = true;
            } else {
                int mid = (l + r) / 2;
                dfs(l, mid, i << 1);
                dfs(mid + 1, r, i << 1 | 1);
            }
        } else {
            for (int k = l; k <= r; k++) {
                ans[k] = false;
            }
        }
        for (int k = 1; k <= unionCnt; k++) {
            undo();
        }
    }

    public static void main(String[] args) {
        FastIO io = new FastIO(System.in, System.out);
        n = io.nextInt();
        m = io.nextInt();
        k = io.nextInt();
        for (int i = 1; i <= n * 2; i++) {
            father[i] = i;
            siz[i] = 1;
        }
        for (int i = 1, x, y, l, r; i <= m; i++) {
            x = io.nextInt();
            y = io.nextInt();
            l = io.nextInt();
            r = io.nextInt();
            add(l + 1, r, x, y, 1, k, 1);
        }
        dfs(1, k, 1);
        for (int i = 1; i <= k; i++) {
            if (ans[i]) {
                io.write("Yes\n");
            } else {
                io.write("No\n");
            }
        }
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
