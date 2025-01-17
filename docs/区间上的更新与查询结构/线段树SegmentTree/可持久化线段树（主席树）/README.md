# 可持久化线段树（主席树）

- 可持久化线段树，又叫主席树。
- 做一次修改操作，就生成一棵新版本线段树，去处理比较复杂的区间查询问题。
- 如果生成 `n` 个版本的线段树，有 `m` 个查询操作，那么单次生成、单次查询的时间复杂度 `O(log n)` 。

## [P3919 【模板】可持久化线段树 1（可持久化数组）](https://www.luogu.com.cn/problem/P3919)

> - **Question 1**
>   - 给定一个长度为 `n` 的数组 `arr` ，下标 `1 ~ n` ，原始数组认为是 `0` 号版本。
>   - 一共有 `m` 条操作，每条操作是如下两种类型中的一种：
>     - `v 1 x y` ：基于 `v` 号版本的数组，把 `x` 位置的值设置成 `y` ，生成新版本的数组。
>     - `v 2 x` ：基于 `v` 号版本的数组，打印 `x` 位置的值，生成新版本的数组和 `v` 版本一致。
>   - 每条操作后得到的新版本数组，版本编号为操作的计数。
>   - **Tips**
>     - `1 <= n, m <= 10^6`

## [P3834 【模板】可持久化线段树 2](https://www.luogu.com.cn/problem/P3834)

> - **Question 2**
>   - 给定一个长度为 `n` 的数组 `arr` ，下标 `1 ~ n` ，一共有 `m` 条查询，每条查询 `l r k` 打印 `arr[l ~ r]` 中第 `k` 小的数字。
>   - **Tips**
>     - `1 <= n、m <= 2 * 10^5`
>     - `0 <= arr[i] <= 10^9`

## [TTM - To the moon](https://www.luogu.com.cn/problem/SP11470)

> - **Question 3**
>   - 给定一个长度为 `n` 的数组 `arr` ，下标 `1 ~ n` ，时间戳 `t = 0` ， `arr` 认为是 `0` 版本的数组，一共有 `m` 条操作，每条操作为如下四种类型中的一种：
>     - `C x y z` ：当前时间戳 `t` 版本的数组， `[x ~ y]` 范围每个数字增加 `z` ，得到 `t + 1` 版本数组，并且 `t++` 。
>     - `Q x y` ：当前时间戳 `t` 版本的数组，打印 `[x ~ y]` 范围累加和。
>     - `H x y z` ： `z` 版本的数组，打印 `[x ~ y]` 范围的累加和。
>     - `B x` ：当前时间戳 `t` 设置成 `x` 。
>   - **Tips**
>     - `-10^9 <= arr[i] <= +10^9`
>     - `1 <= n、m <= 10^5`

## [P3372 【模板】线段树 1](https://www.luogu.com.cn/problem/P3372)

> - **Question 4**
>   - 如题，已知一个数列，你需要进行下面两种操作：
>     - 将某区间每一个数加上 `x` 。
>     - 求出某区间每一个数的和。
>   - **输入描述**
>     - 第一行包含两个正整数 `n, m` ，分别表示该数列数字的个数和操作的总个数。
>     - 第二行包含 `n` 个用空格分隔的整数，其中第 `i` 个数字表示数列第 `i` 项的初始值。
>     - 接下来 `m` 行每行包含 `3` 或 `4` 个整数，表示一个操作，具体如下：
>       - `1 x y k` ：将区间 `[x, y]` 内每个数加上 `k` 。
>       - `2 x y` ：输出区间 `[x, y]` 内每个数的和。
>   - **输出描述**
>     - 输出包含若干行整数，即为所有操作 `2` 的结果。
>   - **Tips**
>     - `1 <= n, m <= 10^5`
>     - 保证任意时刻数列中所有元素的绝对值之和小于等于 `10^8`

## Java

> - **Question 1: 单点修改 + 单点查询**

```java
// 单点修改的可持久化线段树
// 1. 单点修改操作，不需要懒更新机制
// 2. 新版本的线段树生成时，沿途节点新建，其他节点复用，新建的空间为O(log n)
// 3. 查询单点x的信息时，根据版本号，从头节点往下找到x位置对应的节点即可，Question 1
// 4. 查询范围l~r的信息时，利用 r版本的信息 - (l-1)版本的信息 完成查询，Quetion 2
// 5. 总空间O(n * 4 + n * log n)

// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 1000001;

    public static int MAXT = MAXN * 23;

    public static int n, m;

    // 原始数组
    public static int[] arr = new int[MAXN];

    // 可持久化线段树需要
    // root[i] : i号版本线段树的头节点编号
    public static int[] root = new int[MAXN];

    public static int[] left = new int[MAXT];

    public static int[] right = new int[MAXT];

    // value[i] : 节点i的值信息，只有叶节点有这个信息
    public static int[] value = new int[MAXT];

    // 可持久化线段树的节点空间计数
    public static int cnt = 0;

    // 建树，返回头节点编号
    public static int build(int l, int r) {
        int rt = ++cnt;
        if (l == r) {
            value[rt] = arr[l];
        } else {
            int mid = (l + r) >> 1;
            left[rt] = build(l, mid);
            right[rt] = build(mid + 1, r);
        }
        return rt;
    }

    // 线段树范围l~r，信息在i号节点里
    // 在l~r范围上，jobi位置的值，设置成jobv
    // 生成的新节点编号返回
    public static int update(int jobi, int jobv, int l, int r, int i) {
        int rt = ++cnt;
        left[rt] = left[i];
        right[rt] = right[i];
        value[rt] = value[i];
        if (l == r) {
            value[rt] = jobv;
        } else {
            int mid = (l + r) >> 1;
            if (jobi <= mid) {
                left[rt] = update(jobi, jobv, l, mid, left[rt]);
            } else {
                right[rt] = update(jobi, jobv, mid + 1, r, right[rt]);
            }
        }
        return rt;
    }

    // 线段树范围l~r，信息在i号节点里
    // 返回l~r范围上jobi位置的值
    public static int query(int jobi, int l, int r, int i) {
        if (l == r) {
            return value[i];
        }
        int mid = (l + r) >> 1;
        if (jobi <= mid) {
            return query(jobi, l, mid, left[i]);
        } else {
            return query(jobi, mid + 1, r, right[i]);
        }
    }

    public static void main(String[] args) {
        FastIO io = new FastIO(System.in, System.out);
        n = io.nextInt();
        m = io.nextInt();
        for (int i = 1; i <= n; i++) {
            arr[i] = io.nextInt();
        }
        root[0] = build(1, n);
        for (int i = 1, version, op, x, y; i <= m; i++) {
            version = io.nextInt();
            op = io.nextInt();
            x = io.nextInt();
            if (op == 1) {
                y = io.nextInt();
                root[i] = update(x, y, 1, n, root[version]);
            } else {
                root[i] = root[version];
                io.writelnInt(query(x, 1, n, root[i]));
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

> - **Question 2: 单点修改 + 范围查询**

```java
// 值域线段树的离散化

// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 200001;

    public static int MAXT = MAXN * 22;

    public static int n, m, s;

    // 原始数组
    public static int[] arr = new int[MAXN];

    // 收集权值排序并且去重做离散化
    public static int[] sorted = new int[MAXN];

    // 可持久化线段树需要
    // root[i] : 插入arr[i]之后形成新版本的线段树，记录头节点编号
    // 0号版本的线段树代表一个数字也没有时，每种名次的数字出现的次数
    // i号版本的线段树代表arr[1..i]范围内，每种名次的数字出现的次数
    public static int[] root = new int[MAXN];

    public static int[] left = new int[MAXT];

    public static int[] right = new int[MAXT];

    // 排名范围内收集了多少个数字
    public static int[] size = new int[MAXT];

    public static int cnt;

    // 返回num在所有值中排名多少
    public static int kth(int num) {
        int left = 1, right = s, mid, ans = 0;
        while (left <= right) {
            mid = (left + right) / 2;
            if (sorted[mid] <= num) {
                ans = mid;
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return ans;
    }

    // 排名范围l~r，建立线段树，返回头节点编号
    public static int build(int l, int r) {
        int rt = ++cnt;
        size[rt] = 0;
        if (l < r) {
            int mid = (l + r) / 2;
            left[rt] = build(l, mid);
            right[rt] = build(mid + 1, r);
        }
        return rt;
    }

    // 排名范围l~r，信息在i号节点，增加一个排名为jobi的数字
    // 返回新的头节点编号
    public static int insert(int jobi, int l, int r, int i) {
        int rt = ++cnt;
        left[rt] = left[i];
        right[rt] = right[i];
        size[rt] = size[i] + 1;
        if (l < r) {
            int mid = (l + r) / 2;
            if (jobi <= mid) {
                left[rt] = insert(jobi, l, mid, left[rt]);
            } else {
                right[rt] = insert(jobi, mid + 1, r, right[rt]);
            }
        }
        return rt;
    }

    // 排名范围l~r，老版本信息在u号节点，新版本信息在v号节点
    // 返回，第jobk小的数字，排名多少
    public static int query(int jobk, int l, int r, int u, int v) {
        if (l == r) {
            return l;
        }
        int lsize = size[left[v]] - size[left[u]];
        int mid = (l + r) / 2;
        if (lsize >= jobk) {
            return query(jobk, l, mid, left[u], left[v]);
        } else {
            return query(jobk - lsize, mid + 1, r, right[u], right[v]);
        }
    }

    // 权值做离散化并且去重 + 生成各版本的线段树
    public static void prepare() {
        cnt = 0;
        for (int i = 1; i <= n; i++) {
            sorted[i] = arr[i];
        }
        Arrays.sort(sorted, 1, n + 1);
        s = 1;
        for (int i = 2; i <= n; i++) {
            if (sorted[s] != sorted[i]) {
                sorted[++s] = sorted[i];
            }
        }
        root[0] = build(1, s);
        for (int i = 1, x; i <= n; i++) {
            x = kth(arr[i]);
            root[i] = insert(x, 1, s, root[i - 1]);
        }
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        n = (int) in.nval;
        in.nextToken();
        m = (int) in.nval;
        for (int i = 1; i <= n; i++) {
            in.nextToken();
            arr[i] = (int) in.nval;
        }
        prepare();
        for (int i = 1, l, r, k, rank; i <= m; i++) {
            in.nextToken();
            l = (int) in.nval;
            in.nextToken();
            r = (int) in.nval;
            in.nextToken();
            k = (int) in.nval;
            rank = query(k, 1, s, root[l - 1], root[r]);
            out.println(sorted[rank]);
        }
        out.flush();
        out.close();
        br.close();
    }

}
```

> - **Question 3: 范围修改 + 范围查询**

```java
// 范围修改的可持久化线段树

// 经典的方式
// 1. 范围修改操作，需要懒更新机制
// 2. 仿照单点修改的可持久化线段树
// 3. 每来到一个节点，新建节点并且复制老节点的信息
// 4. 当前节点的懒更新下发时(down过程)，左右孩子也新建，接收懒更新信息，务必让老节点信息保持不变

// 生成新版本的线段树，空间占用O(log n)
// 只要有懒更新的下发，必然新建节点，所以生成新版本的线段树、执行查询操作，都会增加空间占用
// 如果生成n个版本的线段树，有m个查询操作，总空间O(n * 4 + n * log n + m * log n)


// 范围增加 + 查询累加和，这一类可持久化线段树，推荐使用标记永久化减少空间占用

// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 100001;

    public static int MAXT = MAXN * 70;

    public static int n, m, t = 0;

    public static int[] arr = new int[MAXN];

    public static int[] root = new int[MAXN];

    public static int[] left = new int[MAXT];

    public static int[] right = new int[MAXT];

    // 累加和信息
    public static long[] sum = new long[MAXT];

    // 懒更新信息，范围增加的懒更新
    public static long[] add = new long[MAXT];

    public static int cnt = 0;

    public static int clone(int i) {
        int rt = ++cnt;
        left[rt] = left[i];
        right[rt] = right[i];
        sum[rt] = sum[i];
        add[rt] = add[i];
        return rt;
    }

    public static void up(int i) {
        sum[i] = sum[left[i]] + sum[right[i]];
    }

    public static void lazy(int i, long v, int n) {
        sum[i] += v * n;
        add[i] += v;
    }

    public static void down(int i, int ln, int rn) {
        if (add[i] != 0) {
            left[i] = clone(left[i]);
            right[i] = clone(right[i]);
            lazy(left[i], add[i], ln);
            lazy(right[i], add[i], rn);
            add[i] = 0;
        }
    }

    public static int build(int l, int r) {
        int rt = ++cnt;
        add[rt] = 0;
        if (l == r) {
            sum[rt] = arr[l];
        } else {
            int mid = (l + r) / 2;
            left[rt] = build(l, mid);
            right[rt] = build(mid + 1, r);
            up(rt);
        }
        return rt;
    }

    public static int add(int jobl, int jobr, long jobv, int l, int r, int i) {
        int rt = clone(i);
        if (jobl <= l && r <= jobr) {
            lazy(rt, jobv, r - l + 1);
        } else {
            int mid = (l + r) / 2;
            down(rt, mid - l + 1, r - mid);
            if (jobl <= mid) {
                left[rt] = add(jobl, jobr, jobv, l, mid, left[rt]);
            }
            if (jobr > mid) {
                right[rt] = add(jobl, jobr, jobv, mid + 1, r, right[rt]);
            }
            up(rt);
        }
        return rt;
    }

    public static long query(int jobl, int jobr, int l, int r, int i) {
        if (jobl <= l && r <= jobr) {
            return sum[i];
        }
        int mid = (l + r) / 2;
        down(i, mid - l + 1, r - mid);
        long ans = 0;
        if (jobl <= mid) {
            ans += query(jobl, jobr, l, mid, left[i]);
        }
        if (jobr > mid) {
            ans += query(jobl, jobr, mid + 1, r, right[i]);
        }
        return ans;
    }

    public static void main(String[] args) throws IOException {
        FastReader in = new FastReader();
        BufferedWriter out = new BufferedWriter(new OutputStreamWriter(System.out));
        n = in.nextInt();
        m = in.nextInt();
        for (int i = 1; i <= n; i++) {
            arr[i] = in.nextInt();
        }
        root[0] = build(1, n);
        String op;
        for (int i = 1, x, y, z; i <= m; i++) {
            op = in.next();
            if (op.equals("C")) {
                x = in.nextInt();
                y = in.nextInt();
                z = in.nextInt();
                root[t + 1] = add(x, y, z, 1, n, root[t]);
                t++;
            } else if (op.equals("Q")) {
                x = in.nextInt();
                y = in.nextInt();
                out.write(query(x, y, 1, n, root[t]) + "\n");
            } else if (op.equals("H")) {
                x = in.nextInt();
                y = in.nextInt();
                z = in.nextInt();
                out.write(query(x, y, 1, n, root[z]) + "\n");
            } else {
                x = in.nextInt();
                t = x;
            }
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

        public boolean hasNext() throws IOException {
            while (hasNextByte()) {
                byte b = buffer[ptr];
                if (!isWhitespace(b))
                    return true;
                ptr++;
            }
            return false;
        }

        public String next() throws IOException {
            byte c;
            do {
                c = readByte();
                if (c == -1)
                    return null;
            } while (c <= ' ');
            StringBuilder sb = new StringBuilder();
            while (c > ' ') {
                sb.append((char) c);
                c = readByte();
            }
            return sb.toString();
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

        public double nextDouble() throws IOException {
            double num = 0, div = 1;
            byte b = readByte();
            while (isWhitespace(b))
                b = readByte();
            boolean minus = false;
            if (b == '-') {
                minus = true;
                b = readByte();
            }
            while (!isWhitespace(b) && b != '.' && b != -1) {
                num = num * 10 + (b - '0');
                b = readByte();
            }
            if (b == '.') {
                b = readByte();
                while (!isWhitespace(b) && b != -1) {
                    num += (b - '0') / (div *= 10);
                    b = readByte();
                }
            }
            return minus ? -num : num;
        }

        private boolean isWhitespace(byte b) {
            return b == ' ' || b == '\n' || b == '\r' || b == '\t';
        }
    }

}
```

> - **Question 4**

```java
// 标记永久化
// 懒更新不再下发，变成只属于某个范围的标记信息，上下级的标记之间，不再相互影响
// 查询时，懒更新也不再下发，从上往下的过程中，维护标记的叠加信息，即可完成查询
// 标记挂在父范围不下发，也能在后续访问时正确合并
// 标记永久化，并不是标记信息的值不再变化，而是上下级标记之间不再相互影响

// 可持久化线段树，可以使用标记永久化可以减少空间占用，但是应用范围比较窄
// 范围增加 + 查询累加和，这一类的线段树，修改和查询的性质都有可叠加性，可以标记永久化
// 这一类的可持久化线段树，出题时会刻意缩减可用空间，目的就是考察标记永久化

// 范围重置、查询最大值/最小值，这一类的线段树，修改和查询的性质不具有可叠加性，就用经典的方式

// 一旦标记永久化，就没有了懒更新的下发，那么查询时就不再新建节点了
// 如果生成n个版本的线段树，有m个查询操作，总空间O(n * 4 + n * log n)

// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 100001;

    public static long[] arr = new long[MAXN];

    // 不是真实累加和，而是之前的任务中
    // 不考虑被上方范围截住的任务，只考虑来到当前范围 或者 往下走的任务
    // 累加和变成了什么
    public static long[] sum = new long[MAXN << 2];

    // 不再是懒更新信息，变成标记信息
    public static long[] addTag = new long[MAXN << 2];

    public static void build(int l, int r, int i) {
        if (l == r) {
            sum[i] = arr[l];
        } else {
            int mid = (l + r) / 2;
            build(l, mid, i << 1);
            build(mid + 1, r, i << 1 | 1);
            sum[i] = sum[i << 1] + sum[i << 1 | 1];
        }
        addTag[i] = 0;
    }

    public static void add(int jobl, int jobr, long jobv, int l, int r, int i) {
        int a = Math.max(jobl, l), b = Math.min(jobr, r);
        sum[i] += jobv * (b - a + 1);
        if (jobl <= l && r <= jobr) {
            addTag[i] += jobv;
        } else {
            int mid = (l + r) / 2;
            if (jobl <= mid) {
                add(jobl, jobr, jobv, l, mid, i << 1);
            }
            if (jobr > mid) {
                add(jobl, jobr, jobv, mid + 1, r, i << 1 | 1);
            }
        }
    }

    public static long query(int jobl, int jobr, long addHistory, int l, int r, int i) {
        if (jobl <= l && r <= jobr) {
            return sum[i] + addHistory * (r - l + 1);
        }
        int mid = (l + r) >> 1;
        long ans = 0;
        if (jobl <= mid) {
            ans += query(jobl, jobr, addHistory + addTag[i], l, mid, i << 1);
        }
        if (jobr > mid) {
            ans += query(jobl, jobr, addHistory + addTag[i], mid + 1, r, i << 1 | 1);
        }
        return ans;
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        int n = (int) in.nval;
        in.nextToken();
        int m = (int) in.nval;
        for (int i = 1; i <= n; i++) {
            in.nextToken();
            arr[i] = (long) in.nval;
        }
        build(1, n, 1);
        int op, jobl, jobr;
        long jobv;
        for (int i = 1; i <= m; i++) {
            in.nextToken();
            op = (int) in.nval;
            if (op == 1) {
                in.nextToken();
                jobl = (int) in.nval;
                in.nextToken();
                jobr = (int) in.nval;
                in.nextToken();
                jobv = (long) in.nval;
                add(jobl, jobr, jobv, 1, n, 1);
            } else {
                in.nextToken();
                jobl = (int) in.nval;
                in.nextToken();
                jobr = (int) in.nval;
                out.println(query(jobl, jobr, 0, 1, n, 1));
            }
        }
        out.flush();
        out.close();
        br.close();
    }

}
```

> - **Question 3**

```java
// 标记永久化减少空间占用，但是仅支持修改和查询的性质都有可叠加性，这一类的可持久化线段树

// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 100001;

    public static int MAXT = MAXN * 25;

    public static int n, m, t = 0;

    public static int[] arr = new int[MAXN];

    public static int[] root = new int[MAXN];

    public static int[] left = new int[MAXT];

    public static int[] right = new int[MAXT];

    // 不是真实累加和，而是之前的任务中
    // 不考虑被上方范围截住的任务，只考虑来到当前范围 或者 往下走的任务
    // 累加和变成了什么
    public static long[] sum = new long[MAXT];

    // 不再是懒更新信息，变成标记信息
    public static long[] addTag = new long[MAXT];

    public static int cnt = 0;

    public static int build(int l, int r) {
        int rt = ++cnt;
        addTag[rt] = 0;
        if (l == r) {
            sum[rt] = arr[l];
        } else {
            int mid = (l + r) / 2;
            left[rt] = build(l, mid);
            right[rt] = build(mid + 1, r);
            sum[rt] = sum[left[rt]] + sum[right[rt]];
        }
        return rt;
    }

    public static int add(int jobl, int jobr, long jobv, int l, int r, int i) {
        int rt = ++cnt, a = Math.max(jobl, l), b = Math.min(jobr, r);
        left[rt] = left[i];
        right[rt] = right[i];
        sum[rt] = sum[i] + jobv * (b - a + 1);
        addTag[rt] = addTag[i];
        if (jobl <= l && r <= jobr) {
            addTag[rt] += jobv;
        } else {
            int mid = (l + r) / 2;
            if (jobl <= mid) {
                left[rt] = add(jobl, jobr, jobv, l, mid, left[rt]);
            }
            if (jobr > mid) {
                right[rt] = add(jobl, jobr, jobv, mid + 1, r, right[rt]);
            }
        }
        return rt;
    }

    public static long query(int jobl, int jobr, long addHistory, int l, int r, int i) {
        if (jobl <= l && r <= jobr) {
            return sum[i] + addHistory * (r - l + 1);
        }
        int mid = (l + r) / 2;
        long ans = 0;
        if (jobl <= mid) {
            ans += query(jobl, jobr, addHistory + addTag[i], l, mid, left[i]);
        }
        if (jobr > mid) {
            ans += query(jobl, jobr, addHistory + addTag[i], mid + 1, r, right[i]);
        }
        return ans;
    }

    public static void main(String[] args) throws IOException {
        FastReader in = new FastReader();
        BufferedWriter out = new BufferedWriter(new OutputStreamWriter(System.out));
        n = in.nextInt();
        m = in.nextInt();
        for (int i = 1; i <= n; i++) {
            arr[i] = in.nextInt();
        }
        root[0] = build(1, n);
        String op;
        for (int i = 1, x, y, z; i <= m; i++) {
            op = in.next();
            if (op.equals("C")) {
                x = in.nextInt();
                y = in.nextInt();
                z = in.nextInt();
                root[t + 1] = add(x, y, z, 1, n, root[t]);
                t++;
            } else if (op.equals("Q")) {
                x = in.nextInt();
                y = in.nextInt();
                out.write(query(x, y, 0, 1, n, root[t]) + "\n");
            } else if (op.equals("H")) {
                x = in.nextInt();
                y = in.nextInt();
                z = in.nextInt();
                out.write(query(x, y, 0, 1, n, root[z]) + "\n");
            } else {
                x = in.nextInt();
                t = x;
            }
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

        public boolean hasNext() throws IOException {
            while (hasNextByte()) {
                byte b = buffer[ptr];
                if (!isWhitespace(b))
                    return true;
                ptr++;
            }
            return false;
        }

        public String next() throws IOException {
            byte c;
            do {
                c = readByte();
                if (c == -1)
                    return null;
            } while (c <= ' ');
            StringBuilder sb = new StringBuilder();
            while (c > ' ') {
                sb.append((char) c);
                c = readByte();
            }
            return sb.toString();
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

        public double nextDouble() throws IOException {
            double num = 0, div = 1;
            byte b = readByte();
            while (isWhitespace(b))
                b = readByte();
            boolean minus = false;
            if (b == '-') {
                minus = true;
                b = readByte();
            }
            while (!isWhitespace(b) && b != '.' && b != -1) {
                num = num * 10 + (b - '0');
                b = readByte();
            }
            if (b == '.') {
                b = readByte();
                while (!isWhitespace(b) && b != -1) {
                    num += (b - '0') / (div *= 10);
                    b = readByte();
                }
            }
            return minus ? -num : num;
        }

        private boolean isWhitespace(byte b) {
            return b == ' ' || b == '\n' || b == '\r' || b == '\t';
        }
    }

}

```
