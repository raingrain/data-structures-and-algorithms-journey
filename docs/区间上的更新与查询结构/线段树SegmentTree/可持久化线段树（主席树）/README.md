# 可持久化线段树（主席树）

- 可持久化线段树，又叫主席树。
- 做一次修改操作，就生成一棵新版本线段树，去处理比较复杂的区间查询问题。
- 如果生成 `n` 个版本的线段树，有 `m` 个查询操作，那么单次生成、单次查询的时间复杂度 `O(log n)` 。

## [Sequence II](https://acm.hdu.edu.cn/showproblem.php?pid=5919)

> - **Question 1**
>   - 给定一个长度为 `n` 的数组 `arr` ，下标 `1 ~ n` ，一共有 `m` 条查询，每条查询格式如下： `l r` 表示 `arr[l ~ r]` 范围上，每个数第一次出现的位置，把这些位置组成一个序列，假设该范围有 `s` 种不同的数，那么序列长度为 `s` ，打印该序列第 `s/2` 个位置（向上取整），对应 `arr` 的什么位置，题目有强制在线的要求，上一次打印的答案为 `lastAns` ，初始时 `lastAns = 0` ，每次给定的 `l` 和 `r` ，按照如下方式得到真实的 `l` 和 `r` ，查询完成后更新 `lastAns` ， `a = (给定l + lastAns) % n + 1, b = (给定r + lastAns) % n + 1, 真实l = min(a, b), 真实r = max(a, b)` 。
>   - **Tips**
>     - `1 <= n、m <= 2 *10^5`
>     - `0 <= arr[i] <= 2* 10^5`

## [P4137 Rmq Problem / mex](https://www.luogu.com.cn/problem/P4137)

> - **Question 2**
>   - 给定一个长度为 `n` 的数组 `arr` ，下标 `1 ~ n` ，一共有 `m` 条查询，每条查询 `l r` ：打印 `arr[l ~ r]` 内没有出现过的最小自然数，注意 `0` 是自然数，请用在线算法解决该问题，因为可以设计强制在线的要求，让离线算法失效。
>   - **Tips**
>     - `1 <= n、m <= 2 *10^5`
>     - `0 <= arr[i] <= 2* 10^5`

## [P2839 [国家集训队] middle](https://www.luogu.com.cn/problem/P2839)

> - **Question 3**
>   - 给定一个长度为 `n` 的数组 `arr` ，下标 `1 ~ n` ，一共有 `m` 条查询，每条查询 `a b c d` : 左端点在 `[a, b]` 之间、右端点在 `[c, d]` 之间，保证 `a < b < c < d` ，哪个区间有最大的上中位数，打印最大的上中位数，题目有强制在线的要求，上一次打印的答案为 `lastAns` ，初始时 `lastAns = 0` ，每次给定四个参数，按照如下方式得到 `a, b, c, d` ，查询完成后更新 `lastAns` ， `(给定的每个参数 + lastAns) % n + 1` ，得到四个值，从小到大对应 `a, b, c, d` 。
>   - **Tips**
>     - `1 <= n <= 20000`
>     - `1 <= m <= 25000`
>     - `1 <= arr[i] <= 10^9`

## [P2633 Count on a tree](https://www.luogu.com.cn/problem/P2633)

> - **Question 4**
>   - 有 `n` 个节点，编号 `1 ~ n` ，每个节点有权值，有 `n - 1` 条边，所有节点组成一棵树，一共有 `m` 条查询，每条查询 `u v k` : 打印 `u` 号点到 `v` 号点的路径上，第 `k` 小的点权，题目有强制在线的要求，上一次打印的答案为 `lastAns` ，初始时 `lastAns = 0` ，每次给定的 `u, v, k` ，按照如下方式得到真实的 `u, v, k` ，查询完成后更新 `lastAns` 。
>   - **Tips**
>     - `真实u = 给定u ^ lastAns`
>     - `真实v = 给定v`
>     - `真实k = 给定k`
>     - `1 <= n、m <= 10^5`
>     - `1 <= arr[i] <= 2^32 - 1`

## [P3899 [湖南集训] 更为厉害](https://www.luogu.com.cn/problem/P3899)

> - **Question 5**
>   - 有 `n` 个节点，编号 `1 ~ n` ，给定 `n - 1` 条边，连成一棵树， `1` 号点是树头，如果 `x` 是 `y` 的祖先节点，认为 `x比y更厉害`，如果 `x` 到 `y` 的路径上，边的数量小于等于某个常数，认为 `x和y是邻居`，一共有 `m` 条查询，每条查询 `a k` ：打印有多少三元组 `(a, b, c)` 满足如下规定：
>     - `a, b, c` 为三个不同的点；
>     - `a` 和 `b` 都比 `c` 厉害；
>     - `a` 和 `b` 是邻居，路径边的数量小于等于给定的 `k` 。
>   - **Tips**
>     - `1 <= n、m、k <= 3 * 10^5`

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

    public static int MAXN = 200002;

    public static int MAXT = MAXN * 37;

    public static int cases, n, m;

    public static int[] arr = new int[MAXN];

    // pos[v] : v这个数字最左出现的位置
    public static int[] pos = new int[MAXN];

    // 可持久化线段树需要
    public static int[] root = new int[MAXN];

    public static int[] left = new int[MAXT];

    public static int[] right = new int[MAXT];

    // 数组范围上只记录每种数第一次出现的位置，这样的位置有多少个
    public static int[] firstSize = new int[MAXT];

    public static int cnt;

    public static int build(int l, int r) {
        int rt = ++cnt;
        if (l == r) {
            return rt;
        }
        int mid = (l + r) / 2;
        left[rt] = build(l, mid);
        right[rt] = build(mid + 1, r);
        return rt;
    }

    public static int update(int jobi, int jobv, int l, int r, int i) {
        int rt = ++cnt;
        left[rt] = left[i];
        right[rt] = right[i];
        firstSize[rt] = firstSize[i] + jobv;
        if (l == r) {
            return rt;
        }
        int mid = (l + r) / 2;
        if (jobi <= mid) {
            left[rt] = update(jobi, jobv, l, mid, left[rt]);
        } else {
            right[rt] = update(jobi, jobv, mid + 1, r, right[rt]);
        }
        return rt;
    }

    public static int querySize(int jobl, int jobr, int l, int r, int i) {
        if (jobl <= l && r <= jobr) {
            return firstSize[i];
        }
        int mid = (l + r) / 2;
        int ans = 0;
        if (jobl <= mid) {
            ans += querySize(jobl, jobr, l, mid, left[i]);
        }
        if (jobr > mid) {
            ans += querySize(jobl, jobr, mid + 1, r, right[i]);
        }
        return ans;
    }

    public static int queryKth(int jobk, int l, int r, int i) {
        if (l == r) {
            return l;
        }
        int mid = (l + r) / 2;
        int leftDiff = firstSize[left[i]];
        if (leftDiff >= jobk) {
            return queryKth(jobk, l, mid, left[i]);
        } else {
            return queryKth(jobk - leftDiff, mid + 1, r, right[i]);
        }
    }

    public static void prepare() {
        cnt = 0;
        Arrays.fill(pos, 0);
        root[n + 1] = build(1, n);
        for (int i = n; i >= 1; i--) {
            if (pos[arr[i]] == 0) {
                root[i] = update(i, 1, 1, n, root[i + 1]);
            } else {
                root[i] = update(pos[arr[i]], -1, 1, n, root[i + 1]);
                root[i] = update(i, 1, 1, n, root[i]);
            }
            pos[arr[i]] = i;
        }
    }

    public static void main(String[] args) throws IOException {
        ReaderWriter io = new ReaderWriter();
        cases = io.nextInt();
        for (int t = 1; t <= cases; t++) {
            n = io.nextInt();
            m = io.nextInt();
            for (int i = 1; i <= n; i++) {
                arr[i] = io.nextInt();
            }
            prepare();
            io.write("Case #");
            io.writeInt(t);
            io.write(":");
            for (int i = 1, a, b, l, r, k, lastAns = 0; i <= m; i++) {
                a = (io.nextInt() + lastAns) % n + 1;
                b = (io.nextInt() + lastAns) % n + 1;
                l = Math.min(a, b);
                r = Math.max(a, b);
                k = (querySize(l, r, 1, n, root[l]) + 1) / 2;
                lastAns = queryKth(k, 1, n, root[l]);
                io.write(" ");
                io.writeInt(lastAns);
            }
            io.write("\n");
        }
        io.flush();
    }

    // 读写工具类
    static class ReaderWriter {
        private static final int BUFFER_SIZE = 1 << 9;
        private byte[] inBuf = new byte[BUFFER_SIZE];
        private int bId, bSize;
        private final java.io.InputStream in;

        private byte[] outBuf = new byte[BUFFER_SIZE];
        private int oId;
        private final java.io.OutputStream out;

        ReaderWriter() {
            in = System.in;
            out = System.out;
        }

        private byte read() throws IOException {
            if (bId == bSize) {
                bSize = in.read(inBuf);
                bId = 0;
                if (bSize == -1)
                    return -1;
            }
            return inBuf[bId++];
        }

        public int nextInt() throws IOException {
            int s = 0;
            byte c = read();
            while (c <= ' ') {
                if (c == -1)
                    return -1;
                c = read();
            }
            boolean neg = (c == '-');
            if (neg)
                c = read();
            while (c >= '0' && c <= '9') {
                s = s * 10 + (c - '0');
                c = read();
            }
            return neg ? -s : s;
        }

        public void write(String s) throws IOException {
            for (int i = 0; i < s.length(); i++) {
                writeByte((byte) s.charAt(i));
            }
        }

        public void writeInt(int x) throws IOException {
            if (x == 0) {
                writeByte((byte) '0');
                return;
            }
            if (x < 0) {
                writeByte((byte) '-');
                x = -x;
            }
            int len = 0;
            byte[] tmp = new byte[12];
            while (x > 0) {
                tmp[len++] = (byte) ((x % 10) + '0');
                x /= 10;
            }
            while (len-- > 0) {
                writeByte(tmp[len]);
            }
        }

        private void writeByte(byte b) throws IOException {
            if (oId == BUFFER_SIZE) {
                flush();
            }
            outBuf[oId++] = b;
        }

        public void flush() throws IOException {
            if (oId > 0) {
                out.write(outBuf, 0, oId);
                oId = 0;
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

    public static int n, m;

    public static int[] arr = new int[MAXN];

    public static int[] root = new int[MAXN];

    public static int[] left = new int[MAXT];

    public static int[] right = new int[MAXT];

    // last[i] : l~r的信息存在last[i]
    // 表示该范围每个数字最后出现的位置中，最左在哪
    public static int[] lateLeft = new int[MAXT];

    public static int cnt;

    public static int build(int l, int r) {
        int rt = ++cnt;
        lateLeft[rt] = 0;
        if (l < r) {
            int mid = (l + r) / 2;
            left[rt] = build(l, mid);
            right[rt] = build(mid + 1, r);
        }
        return rt;
    }

    // l~r范围，信息在i位置，jobi这个数字，最新出现在jobv位置
    // 返回新的头节点编号
    public static int update(int jobi, int jobv, int l, int r, int i) {
        int rt = ++cnt;
        left[rt] = left[i];
        right[rt] = right[i];
        lateLeft[rt] = lateLeft[i];
        if (l == r) {
            lateLeft[rt] = jobv;
        } else {
            int mid = (l + r) / 2;
            if (jobi <= mid) {
                left[rt] = update(jobi, jobv, l, mid, left[rt]);
            } else {
                right[rt] = update(jobi, jobv, mid + 1, r, right[rt]);
            }
            lateLeft[rt] = Math.min(lateLeft[left[rt]], lateLeft[right[rt]]);
        }
        return rt;
    }

    // 数字在l~r范围上，没有出现的最小自然数，课上重点图解
    public static int query(int pos, int l, int r, int i) {
        if (l == r) {
            return l;
        }
        int mid = (l + r) / 2;
        if (lateLeft[left[i]] < pos) {
            // l...mid范围上，每个数字最晚出现的位置中
            // 最左的位置如果在pos以左，说明l...mid范围上，一定有缺失的数字
            return query(pos, l, mid, left[i]);
        } else {
            // 缺失的数字一定在mid+1....r范围
            // 因为l...r一定有缺失的数字才会来到这个范围的
            // 如果左侧不缺失，那缺失的数字一定在右侧范围上
            return query(pos, mid + 1, r, right[i]);
        }
    }

    public static void prepare() {
        cnt = 0;
        root[0] = build(0, n);
        for (int i = 1; i <= n; i++) {
            if (arr[i] >= n) {
                root[i] = root[i - 1];
            } else {
                root[i] = update(arr[i], i, 0, n, root[i - 1]);
            }
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
        for (int i = 1, l, r; i <= m; i++) {
            in.nextToken();
            l = (int) in.nval;
            in.nextToken();
            r = (int) in.nval;
            out.println(query(l, 0, n, root[r]));
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

    public static int MAXN = 20001;

    public static int MAXT = MAXN * 20;

    public static int INF = 10000001;

    public static int n, m;

    // 原始位置、数值
    public static int[][] arr = new int[MAXN][2];

    // 可持久化线段树需要
    public static int[] root = new int[MAXN];

    public static int[] left = new int[MAXT];

    public static int[] right = new int[MAXT];

    // 区间内最大前缀和，前缀不能为空
    public static int[] pre = new int[MAXT];

    // 区间内最大后缀和，后缀不能为空
    public static int[] suf = new int[MAXT];

    // 区间内累加和，区间为空认为累加和是0
    public static int[] sum = new int[MAXT];

    public static int cnt;

    // 查询的问题，a、b、c、d
    public static int[] ques = new int[4];

    // 收集区间信息，pre、suf、sum
    public static int[] info = new int[3];

    public static int build(int l, int r) {
        int rt = ++cnt;
        pre[rt] = suf[rt] = sum[rt] = r - l + 1;
        if (l < r) {
            int mid = (l + r) / 2;
            left[rt] = build(l, mid);
            right[rt] = build(mid + 1, r);
        }
        return rt;
    }

    public static void up(int i) {
        pre[i] = Math.max(pre[left[i]], sum[left[i]] + pre[right[i]]);
        suf[i] = Math.max(suf[right[i]], suf[left[i]] + sum[right[i]]);
        sum[i] = sum[left[i]] + sum[right[i]];
    }

    public static int update(int jobi, int l, int r, int i) {
        int rt = ++cnt;
        left[rt] = left[i];
        right[rt] = right[i];
        pre[rt] = pre[i];
        suf[rt] = suf[i];
        sum[rt] = sum[i];
        if (l == r) {
            pre[rt] = suf[rt] = sum[rt] = -1;
        } else {
            int mid = (l + r) / 2;
            if (jobi <= mid) {
                left[rt] = update(jobi, l, mid, left[rt]);
            } else {
                right[rt] = update(jobi, mid + 1, r, right[rt]);
            }
            up(rt);
        }
        return rt;
    }

    public static void initInfo() {
        info[0] = info[1] = -INF;
        info[2] = 0;
    }

    public static void mergeRight(int r) {
        info[0] = Math.max(info[0], info[2] + pre[r]);
        info[1] = Math.max(suf[r], info[1] + sum[r]);
        info[2] += sum[r];
    }

    public static void query(int jobl, int jobr, int l, int r, int i) {
        if (jobl <= l && r <= jobr) {
            mergeRight(i);
        } else {
            int mid = (l + r) / 2;
            if (jobl <= mid) {
                query(jobl, jobr, l, mid, left[i]);
            }
            if (jobr > mid) {
                query(jobl, jobr, mid + 1, r, right[i]);
            }
        }
    }

    public static void prepare() {
        Arrays.sort(arr, 1, n + 1, (a, b) -> a[1] - b[1]);
        cnt = 0;
        root[1] = build(1, n);
        for (int i = 2; i <= n; i++) {
            root[i] = update(arr[i - 1][0], 1, n, root[i - 1]);
        }
    }

    public static boolean check(int a, int b, int c, int d, int v) {
        initInfo();
        query(a, b, 1, n, root[v]);
        int best = info[1];
        initInfo();
        query(c, d, 1, n, root[v]);
        best += info[0];
        if (b + 1 <= c - 1) {
            initInfo();
            query(b + 1, c - 1, 1, n, root[v]);
            best += info[2];
        }
        return best >= 0;
    }

    public static int compute(int a, int b, int c, int d) {
        int left = 1, right = n, mid, ans = 0;
        while (left <= right) {
            mid = (left + right) / 2;
            if (check(a, b, c, d, mid)) {
                ans = arr[mid][1];
                left = mid + 1;
            } else {
                right = mid - 1;
            }
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
            arr[i][0] = i;
            in.nextToken();
            arr[i][1] = (int) in.nval;
        }
        prepare();
        in.nextToken();
        m = (int) in.nval;
        for (int i = 1, lastAns = 0; i <= m; i++) {
            in.nextToken();
            ques[0] = ((int) in.nval + lastAns) % n + 1;
            in.nextToken();
            ques[1] = ((int) in.nval + lastAns) % n + 1;
            in.nextToken();
            ques[2] = ((int) in.nval + lastAns) % n + 1;
            in.nextToken();
            ques[3] = ((int) in.nval + lastAns) % n + 1;
            Arrays.sort(ques);
            lastAns = compute(ques[0], ques[1], ques[2], ques[3]);
            out.println(lastAns);
        }
        out.flush();
        out.close();
        br.close();
    }

}
```

> - **Question 4: 标记永久化**

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

    public static int MAXH = 20;

    public static int MAXT = MAXN * MAXH;

    public static int n, m, s;

    // 各个节点权值
    public static int[] arr = new int[MAXN];

    // 收集权值排序并且去重做离散化
    public static int[] sorted = new int[MAXN];

    // 链式前向星需要
    public static int[] head = new int[MAXN];

    public static int[] to = new int[MAXN << 1];

    public static int[] next = new int[MAXN << 1];

    public static int cntg = 0;

    // 可持久化线段树需要
    public static int[] root = new int[MAXN];

    public static int[] left = new int[MAXT];

    public static int[] right = new int[MAXT];

    public static int[] size = new int[MAXT];

    public static int cntt = 0;

    // 树上倍增找lca需要
    public static int[] deep = new int[MAXN];

    public static int[][] stjump = new int[MAXN][MAXH];

    public static int kth(int num) {
        int left = 1, right = s, mid;
        while (left <= right) {
            mid = (left + right) / 2;
            if (sorted[mid] == num) {
                return mid;
            } else if (sorted[mid] < num) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return -1;
    }

    public static int build(int l, int r) {
        int rt = ++cntt;
        size[rt] = 0;
        if (l < r) {
            int mid = (l + r) / 2;
            left[rt] = build(l, mid);
            right[rt] = build(mid + 1, r);
        }
        return rt;
    }

    public static void prepare() {
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
    }

    public static void addEdge(int u, int v) {
        next[++cntg] = head[u];
        to[cntg] = v;
        head[u] = cntg;
    }

    public static int insert(int jobi, int l, int r, int i) {
        int rt = ++cntt;
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

    public static int query(int jobk, int l, int r, int u, int v, int lca, int lcafa) {
        if (l == r) {
            return l;
        }
        int lsize = size[left[u]] + size[left[v]] - size[left[lca]] - size[left[lcafa]];
        int mid = (l + r) / 2;
        if (lsize >= jobk) {
            return query(jobk, l, mid, left[u], left[v], left[lca], left[lcafa]);
        } else {
            return query(jobk - lsize, mid + 1, r, right[u], right[v], right[lca], right[lcafa]);
        }
    }

    // 递归版，C++可以通过，java无法通过，递归会爆栈
    public static void dfs1(int u, int f) {
        root[u] = insert(kth(arr[u]), 1, s, root[f]);
        deep[u] = deep[f] + 1;
        stjump[u][0] = f;
        for (int p = 1; p < MAXH; p++) {
            stjump[u][p] = stjump[stjump[u][p - 1]][p - 1];
        }
        for (int ei = head[u]; ei > 0; ei = next[ei]) {
            if (to[ei] != f) {
                dfs1(to[ei], u);
            }
        }
    }

    // 迭代版，都可以通过
    // 讲解118，详解了从递归版改迭代版
    public static int[][] ufe = new int[MAXN][3];

    public static int stackSize, u, f, e;

    public static void push(int u, int f, int e) {
        ufe[stackSize][0] = u;
        ufe[stackSize][1] = f;
        ufe[stackSize][2] = e;
        stackSize++;
    }

    public static void pop() {
        --stackSize;
        u = ufe[stackSize][0];
        f = ufe[stackSize][1];
        e = ufe[stackSize][2];
    }

    // dfs1的迭代版
    public static void dfs2() {
        stackSize = 0;
        push(1, 0, -1);
        while (stackSize > 0) {
            pop();
            if (e == -1) {
                root[u] = insert(kth(arr[u]), 1, s, root[f]);
                deep[u] = deep[f] + 1;
                stjump[u][0] = f;
                for (int p = 1; p < MAXH; p++) {
                    stjump[u][p] = stjump[stjump[u][p - 1]][p - 1];
                }
                e = head[u];
            } else {
                e = next[e];
            }
            if (e != 0) {
                push(u, f, e);
                if (to[e] != f) {
                    push(to[e], u, -1);
                }
            }
        }
    }

    public static int lca(int a, int b) {
        if (deep[a] < deep[b]) {
            int tmp = a;
            a = b;
            b = tmp;
        }
        for (int p = MAXH - 1; p >= 0; p--) {
            if (deep[stjump[a][p]] >= deep[b]) {
                a = stjump[a][p];
            }
        }
        if (a == b) {
            return a;
        }
        for (int p = MAXH - 1; p >= 0; p--) {
            if (stjump[a][p] != stjump[b][p]) {
                a = stjump[a][p];
                b = stjump[b][p];
            }
        }
        return stjump[a][0];
    }

    public static int kth(int u, int v, int k) {
        int lca = lca(u, v);
        int i = query(k, 1, s, root[u], root[v], root[lca], root[stjump[lca][0]]);
        return sorted[i];
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
        for (int i = 1, u, v; i < n; i++) {
            in.nextToken();
            u = (int) in.nval;
            in.nextToken();
            v = (int) in.nval;
            addEdge(u, v);
            addEdge(v, u);
        }
        dfs2(); // 使用迭代版防止爆栈
        for (int i = 1, u, v, k, lastAns = 0; i <= m; i++) {
            in.nextToken();
            u = (int) in.nval ^ lastAns;
            in.nextToken();
            v = (int) in.nval;
            in.nextToken();
            k = (int) in.nval;
            lastAns = kth(u, v, k);
            out.println(lastAns);
        }
        out.flush();
        out.close();
        br.close();
    }

}
```

> - **Question 5: 范围修改 + 标记永久化**

```java
// 标记永久化减少空间占用，但是仅支持修改和查询的性质都有可叠加性，这一类的可持久化线段树

// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 300001;

    public static int MAXT = MAXN * 22;

    public static int n, m;

    // 链式前向星需要
    public static int[] head = new int[MAXN];

    public static int[] to = new int[MAXN << 1];

    public static int[] next = new int[MAXN << 1];

    public static int cntg = 0;

    // 可持久化线段树需要
    public static int[] root = new int[MAXN];

    public static int[] left = new int[MAXT];

    public static int[] right = new int[MAXT];

    public static long[] sum = new long[MAXT];

    public static int cntt = 0;

    // dfs需要
    // deep[i] : i号节点的深度
    public static int[] deep = new int[MAXN];

    // size[i] : 以i号节点为头的树，有多少个节点
    public static int[] size = new int[MAXN];

    // dfn[i] : i号节点的dfn序号
    public static int[] dfn = new int[MAXN];

    public static int cntd = 0;

    public static void addEdge(int u, int v) {
        next[++cntg] = head[u];
        to[cntg] = v;
        head[u] = cntg;
    }

    public static int build(int l, int r) {
        int rt = ++cntt;
        sum[rt] = 0;
        if (l < r) {
            int mid = (l + r) / 2;
            left[rt] = build(l, mid);
            right[rt] = build(mid + 1, r);
        }
        return rt;
    }

    public static int add(int jobi, long jobv, int l, int r, int i) {
        int rt = ++cntt;
        left[rt] = left[i];
        right[rt] = right[i];
        sum[rt] = sum[i] + jobv;
        if (l < r) {
            int mid = (l + r) / 2;
            if (jobi <= mid) {
                left[rt] = add(jobi, jobv, l, mid, left[rt]);
            } else {
                right[rt] = add(jobi, jobv, mid + 1, r, right[rt]);
            }
        }
        return rt;
    }

    public static long query(int jobl, int jobr, int l, int r, int u, int v) {
        if (jobl <= l && r <= jobr) {
            return sum[v] - sum[u];
        }
        long ans = 0;
        int mid = (l + r) / 2;
        if (jobl <= mid) {
            ans += query(jobl, jobr, l, mid, left[u], left[v]);
        }
        if (jobr > mid) {
            ans += query(jobl, jobr, mid + 1, r, right[u], right[v]);
        }
        return ans;
    }

    // 递归版，C++可以通过，java无法通过，递归会爆栈
    public static void dfs1(int u, int f) {
        deep[u] = deep[f] + 1;
        size[u] = 1;
        dfn[u] = ++cntd;
        for (int ei = head[u]; ei > 0; ei = next[ei]) {
            if (to[ei] != f) {
                dfs1(to[ei], u);
            }
        }
        for (int ei = head[u]; ei > 0; ei = next[ei]) {
            if (to[ei] != f) {
                size[u] += size[to[ei]];
            }
        }
    }

    // 递归版，C++可以通过，java无法通过，递归会爆栈
    public static void dfs2(int u, int f) {
        root[dfn[u]] = add(deep[u], size[u] - 1, 1, n, root[dfn[u] - 1]);
        for (int ei = head[u]; ei > 0; ei = next[ei]) {
            if (to[ei] != f) {
                dfs2(to[ei], u);
            }
        }
    }

    // dfs1、dfs2，分别改成迭代版，dfs3、dfs4
    // 讲解118，详解了从递归版改迭代版
    public static int[][] ufe = new int[MAXN][3];

    public static int stackSize, u, f, e;

    public static void push(int u, int f, int e) {
        ufe[stackSize][0] = u;
        ufe[stackSize][1] = f;
        ufe[stackSize][2] = e;
        stackSize++;
    }

    public static void pop() {
        --stackSize;
        u = ufe[stackSize][0];
        f = ufe[stackSize][1];
        e = ufe[stackSize][2];
    }

    // dfs1的迭代版
    public static void dfs3() {
        stackSize = 0;
        push(1, 0, -1);
        while (stackSize > 0) {
            pop();
            if (e == -1) {
                deep[u] = deep[f] + 1;
                size[u] = 1;
                dfn[u] = ++cntd;
                e = head[u];
            } else {
                e = next[e];
            }
            if (e != 0) {
                push(u, f, e);
                if (to[e] != f) {
                    push(to[e], u, -1);
                }
            } else {
                for (int ei = head[u]; ei > 0; ei = next[ei]) {
                    if (to[ei] != f) {
                        size[u] += size[to[ei]];
                    }
                }
            }
        }
    }

    // dfs2的迭代版
    public static void dfs4() {
        stackSize = 0;
        push(1, 0, -1);
        while (stackSize > 0) {
            pop();
            if (e == -1) {
                root[dfn[u]] = add(deep[u], size[u] - 1, 1, n, root[dfn[u] - 1]);
                e = head[u];
            } else {
                e = next[e];
            }
            if (e != 0) {
                push(u, f, e);
                if (to[e] != f) {
                    push(to[e], u, -1);
                }
            }
        }
    }

    public static long compute(int a, int k) {
        long ans = query(deep[a] + 1, deep[a] + k, 1, n, root[dfn[a] - 1], root[dfn[a] + size[a] - 1]);
        ans += (long) (size[a] - 1) * Math.min(k, deep[a] - 1);
        return ans;
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        n = (int) in.nval;
        in.nextToken();
        m = (int) in.nval;
        for (int i = 1, u, v; i < n; i++) {
            in.nextToken();
            u = (int) in.nval;
            in.nextToken();
            v = (int) in.nval;
            addEdge(u, v);
            addEdge(v, u);
        }
        root[0] = build(1, n);
        dfs3(); // 使用迭代版防止爆栈
        dfs4(); // 使用迭代版防止爆栈
        for (int i = 1, a, k; i <= m; i++) {
            in.nextToken();
            a = (int) in.nval;
            in.nextToken();
            k = (int) in.nval;
            out.println(compute(a, k));
        }
        out.flush();
        out.close();
        br.close();
    }

}
```
