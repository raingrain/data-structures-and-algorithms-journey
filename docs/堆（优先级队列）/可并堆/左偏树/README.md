# 左偏树

左偏树是一种可并堆结构，支持堆的合并，两个堆的合并过程，时间复杂度 `O(log n)` ，方便进行可持久化。

## [P3377 【模板】左偏树/可并堆](https://www.luogu.com.cn/problem/P3377)

> - **Question 1**
>   - 依次给定 `n` 个非负数字，表示有 `n` 个小根堆，每个堆只有一个数，实现如下两种操作，操作一共调用 `m` 次：
>     - `1 x y` ：第 `x` 个数字所在的堆和第 `y` 个数字所在的堆合并，如果两个数字已经在一个堆或者某个数字已经删除，不进行合并。
>     - `2 x` ：打印第 `x` 个数字所在堆的最小值，并且在堆里删掉这个最小值，如果第 `x` 个数字已经被删除，也就是找不到所在的堆，打印 `-1` ，若有多个最小值，优先删除编号小的。
>   - **Tips**
>     - `1 <= n, m <= 10^5`

## [P2713 罗马游戏](https://www.luogu.com.cn/problem/P2713)

> - **Question 2**
>   - 依次给定 `n` 个非负数字，表示有 `n` 个小根堆，每个堆只有一个数，实现如下两种操作，操作一共调用 `m` 次：
>     - `M x y` ：第 `x` 个数字所在的堆和第 `y` 个数字所在的堆合并，如果两个数字已经在一个堆或者某个数字已经删除，不进行合并。
>     - `K x` ：打印第 `x` 个数字所在堆的最小值，并且在堆里删掉这个最小值，如果第 `x` 个数字已经被删除，也就是找不到所在的堆，打印 `0` ，若有多个最小值，优先删除编号小的。
>   - **Tips**
>     - `1 <= n <= 10^6`
>     - `1 <= m <= 10^5`

## Java

> - **Question 1**

```java
// 左偏树表示的堆结构，不是完全二叉树，而是广义的堆结构，平衡性由dist值来维护
// 节点的dist值表示，离开该节点后，至少经过几个节点能到达空，dist[0] = -1
// 合并后如果发现，左儿子dist值 < 右儿子dist值，就交换左右树
// 左偏树的每个节点满足，左儿子dist值 >= 右儿子dist值，节点dist值 = 右儿子dist值 + 1

// 左偏树合并的过程，重点是理解合并总发生在右树上，以及交换左右树的过程

// 左偏树的平衡性，只是方便了合并的过程，树上依然可能出现很长的链，所以查询快需要结合并查集
// 左偏树结构，合并操作非常方便，但是树上依然可能出现很长的链
// 任何节点找堆顶的过程，不能依赖左偏树的结构，所以建立并查集

// 利用并查集压缩路径的特点，任何节点找堆顶的过程就得到了加速
// 需要保证，并查集最上方的代表节点 = 左偏树的头也就是堆顶

// 左偏树删除堆顶节点
// 1，堆顶节点的左右树合并，形成新树
// 2，堆顶节点的left、right、dist信息清空
// 3，堆顶节点的father指向新树的头，很重要

// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 100001;

    public static int n, m;

    // 左偏树需要
    public static int[] num = new int[MAXN];

    public static int[] left = new int[MAXN];

    public static int[] right = new int[MAXN];

    public static int[] dist = new int[MAXN];

    // 并查集需要father数组，方便快速找到树的头
    // father[i]不代表i在树上的父亲节点
    // father是并查集找代表节点的路径信息
    // 需要保证，并查集最上方的代表节点 = 树的头节点
    public static int[] father = new int[MAXN];

    public static void prepare() {
        dist[0] = -1;
        for (int i = 1; i <= n; i++) {
            left[i] = right[i] = dist[i] = 0;
            father[i] = i;
        }
    }

    public static int find(int i) {
        father[i] = father[i] == i ? i : find(father[i]);
        return father[i];
    }

    // 编号为i的左偏树 与 编号为j的左偏树合并，返回新树的头节点编号
    public static int merge(int i, int j) {
        if (i == 0 || j == 0) {
            return i + j;
        }
        int tmp;
        // 维护小根堆，如果值一样，编号小的节点做头
        if (num[i] > num[j] || (num[i] == num[j] && i > j)) {
            tmp = i;
            i = j;
            j = tmp;
        }
        right[i] = merge(right[i], j);
        if (dist[left[i]] < dist[right[i]]) {
            tmp = left[i];
            left[i] = right[i];
            right[i] = tmp;
        }
        dist[i] = dist[right[i]] + 1;
        father[left[i]] = father[right[i]] = i;
        return i;
    }

    // 节点i一定是左偏树的头，在左偏树上删掉节点i，返回新树的头节点编号
    public static int pop(int i) {
        father[left[i]] = left[i];
        father[right[i]] = right[i];
        // 并查集有路径压缩，所以i下方的某个节点x，可能有father[x] = i
        // 现在要删掉i了，所以x往上会找不到正确的头节点
        // 为了任何节点往上都能找到正确的头，所以要有下面这句
        father[i] = merge(left[i], right[i]);
        left[i] = right[i] = dist[i] = 0;
        return father[i];
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        n = (int) in.nval;
        in.nextToken();
        m = (int) in.nval;
        prepare();
        for (int i = 1; i <= n; i++) {
            in.nextToken();
            num[i] = (int) in.nval;
        }
        for (int i = 1, op, x, y; i <= m; i++) {
            in.nextToken();
            op = (int) in.nval;
            if (op == 1) {
                in.nextToken();
                x = (int) in.nval;
                in.nextToken();
                y = (int) in.nval;
                if (num[x] != -1 && num[y] != -1) {
                    int l = find(x);
                    int r = find(y);
                    if (l != r) {
                        merge(l, r);
                    }
                }
            } else {
                in.nextToken();
                x = (int) in.nval;
                if (num[x] == -1) {
                    out.println(-1);
                } else {
                    int ans = find(x);
                    out.println(num[ans]);
                    pop(ans);
                    num[ans] = -1;
                }
            }
        }
        out.flush();
        out.close();
        br.close();
    }

}
```

> - **Question 2: 数据量增强**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 1000001;

    public static int n, m;

    public static int[] num = new int[MAXN];

    public static int[] left = new int[MAXN];

    public static int[] right = new int[MAXN];

    public static int[] dist = new int[MAXN];

    public static int[] father = new int[MAXN];

    public static void prepare() {
        dist[0] = -1;
        for (int i = 1; i <= n; i++) {
            left[i] = right[i] = dist[i] = 0;
            father[i] = i;
        }
    }

    public static int find(int i) {
        father[i] = father[i] == i ? i : find(father[i]);
        return father[i];
    }

    public static int merge(int i, int j) {
        if (i == 0 || j == 0) {
            return i + j;
        }
        int tmp;
        if (num[i] > num[j] || (num[i] == num[j] && i > j)) {
            tmp = i;
            i = j;
            j = tmp;
        }
        right[i] = merge(right[i], j);
        if (dist[left[i]] < dist[right[i]]) {
            tmp = left[i];
            left[i] = right[i];
            right[i] = tmp;
        }
        dist[i] = dist[right[i]] + 1;
        father[left[i]] = father[right[i]] = i;
        return i;
    }

    public static int pop(int i) {
        father[left[i]] = left[i];
        father[right[i]] = right[i];
        father[i] = merge(left[i], right[i]);
        left[i] = right[i] = dist[i] = 0;
        return father[i];
    }

    public static void main(String[] args) {
        FastReader in = new FastReader(System.in);
        FastWriter out = new FastWriter(System.out);
        n = in.readInt();
        prepare();
        for (int i = 1; i <= n; i++) {
            num[i] = in.readInt();
        }
        m = in.readInt();
        String op;
        for (int i = 1, x, y; i <= m; i++) {
            op = in.readString();
            if (op.equals("M")) {
                x = in.readInt();
                y = in.readInt();
                if (num[x] != -1 && num[y] != -1) {
                    int l = find(x);
                    int r = find(y);
                    if (l != r) {
                        merge(l, r);
                    }
                }
            } else {
                x = in.readInt();
                if (num[x] == -1) {
                    out.println(0);
                } else {
                    int ans = find(x);
                    out.println(num[ans]);
                    pop(ans);
                    num[ans] = -1;
                }
            }
        }
        out.flush();
        out.close();
    }

    // 快读
    public static class FastReader {
        InputStream is;
        private byte[] inbuf = new byte[1024];
        public int lenbuf = 0;
        public int ptrbuf = 0;

        public FastReader(final InputStream is) {
            this.is = is;
        }

        public String readString() {
            char cur;
            do {
                cur = (char) readByte();
            } while (cur == ' ' || cur == '\n');
            StringBuilder builder = new StringBuilder();
            while (cur != ' ' && cur != '\n') {
                builder.append(cur);
                cur = (char) readByte();
            }
            return builder.toString();
        }

        public int readByte() {
            if (lenbuf == -1) {
                throw new InputMismatchException();
            }
            if (ptrbuf >= lenbuf) {
                ptrbuf = 0;
                try {
                    lenbuf = is.read(inbuf);
                } catch (IOException e) {
                    throw new InputMismatchException();
                }
                if (lenbuf <= 0) {
                    return -1;
                }
            }
            return inbuf[ptrbuf++];
        }

        public int readInt() {
            return (int) readLong();
        }

        public long readLong() {
            long num = 0;
            int b;
            boolean minus = false;
            while ((b = readByte()) != -1 && !((b >= '0' && b <= '9') || b == '-'))
                ;
            if (b == '-') {
                minus = true;
                b = readByte();
            }

            while (true) {
                if (b >= '0' && b <= '9') {
                    num = num * 10 + (b - '0');
                } else {
                    return minus ? -num : num;
                }
                b = readByte();
            }
        }
    }

    // 快写
    public static class FastWriter {
        private static final int BUF_SIZE = 1 << 13;
        private final byte[] buf = new byte[BUF_SIZE];
        private OutputStream out;
        private Writer writer;
        private int ptr = 0;

        public FastWriter(Writer writer) {
            this.writer = new BufferedWriter(writer);
            out = new ByteArrayOutputStream();
        }

        public FastWriter(OutputStream os) {
            this.out = os;
        }

        public FastWriter(String path) {
            try {
                this.out = new FileOutputStream(path);
            } catch (FileNotFoundException e) {
                throw new RuntimeException("FastWriter");
            }
        }

        public FastWriter write(byte b) {
            buf[ptr++] = b;
            if (ptr == BUF_SIZE) {
                innerflush();
            }
            return this;
        }

        public FastWriter write(String s) {
            s.chars().forEach(c -> {
                buf[ptr++] = (byte) c;
                if (ptr == BUF_SIZE) {
                    innerflush();
                }
            });
            return this;
        }

        private static int countDigits(long l) {
            if (l >= 1000000000000000000L) {
                return 19;
            }
            if (l >= 100000000000000000L) {
                return 18;
            }
            if (l >= 10000000000000000L) {
                return 17;
            }
            if (l >= 1000000000000000L) {
                return 16;
            }
            if (l >= 100000000000000L) {
                return 15;
            }
            if (l >= 10000000000000L) {
                return 14;
            }
            if (l >= 1000000000000L) {
                return 13;
            }
            if (l >= 100000000000L) {
                return 12;
            }
            if (l >= 10000000000L) {
                return 11;
            }
            if (l >= 1000000000L) {
                return 10;
            }
            if (l >= 100000000L) {
                return 9;
            }
            if (l >= 10000000L) {
                return 8;
            }
            if (l >= 1000000L) {
                return 7;
            }
            if (l >= 100000L) {
                return 6;
            }
            if (l >= 10000L) {
                return 5;
            }
            if (l >= 1000L) {
                return 4;
            }
            if (l >= 100L) {
                return 3;
            }
            if (l >= 10L) {
                return 2;
            }
            return 1;
        }

        public FastWriter write(long x) {
            if (x == Long.MIN_VALUE) {
                return write("" + x);
            }
            if (ptr + 21 >= BUF_SIZE) {
                innerflush();
            }
            if (x < 0) {
                write((byte) '-');
                x = -x;
            }
            int d = countDigits(x);
            for (int i = ptr + d - 1; i >= ptr; i--) {
                buf[i] = (byte) ('0' + x % 10);
                x /= 10;
            }
            ptr += d;
            return this;
        }

        public FastWriter writeln(long x) {
            return write(x).writeln();
        }

        public FastWriter writeln() {
            return write((byte) '\n');
        }

        private void innerflush() {
            try {
                out.write(buf, 0, ptr);
                ptr = 0;
            } catch (IOException e) {
                throw new RuntimeException("innerflush");
            }
        }

        public void flush() {
            innerflush();
            try {
                if (writer != null) {
                    writer.write(((ByteArrayOutputStream) out).toString());
                    out = new ByteArrayOutputStream();
                    writer.flush();
                } else {
                    out.flush();
                }
            } catch (IOException e) {
                throw new RuntimeException("flush");
            }
        }

        public FastWriter println(long x) {
            return writeln(x);
        }

        public void close() {
            flush();
            try {
                out.close();
            } catch (Exception e) {
            }
        }

    }

}
```
