# [POI 2011] ROT-Tree Rotations

## [P3521 [POI 2011] ROT-Tree Rotations](https://www.luogu.com.cn/problem/P3521)

> - **Question**
>   - 给定数字 `n` ，表示一棵二叉树有 `n` 个叶节点，叶节点的权值都不同并且都是 `1 ~ n` 范围的数字，树上的任何节点，要么是叶节点，要么一定有两个孩子，请从输入流中不断读取数字 `x` 来建树，如果 `x != 0` ，表示当前来到叶节点并且权值为 `x` ，注意只有叶节点有权值，如果 `x == 0` ，表示当前来到非叶节点，先递归建立左树，再递归建立右树，输入流保证根据规则可以建好这棵二叉树，你可以任选一些节点，交换这些节点的左右子树，目的是先序遍历整棵树之后，所有叶节点权值组成的序列中，逆序对数量尽可能小，打印这个最小值。
>   - **Tips**
>     - `2 <= n <= 2 * 10^5`

## Java

> - **Question**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXT = 5000001;
    public static int n;

    public static int[] ls = new int[MAXT];
    public static int[] rs = new int[MAXT];
    public static int[] siz = new int[MAXT];
    public static int cntt;

    public static long ans;

    public static void up(int i) {
        siz[i] = siz[ls[i]] + siz[rs[i]];
    }

    public static int build(int jobi, int l, int r) {
        int rt = ++cntt;
        if (l == r) {
            siz[rt]++;
        } else {
            int mid = (l + r) >> 1;
            if (jobi <= mid) {
                ls[rt] = build(jobi, l, mid);
            } else {
                rs[rt] = build(jobi, mid + 1, r);
            }
            up(rt);
        }
        return rt;
    }

    public static int no, yes;

    public static int merge(int l, int r, int t1, int t2) {
        if (t1 == 0 || t2 == 0) {
            return t1 + t2;
        }
        if (l == r) {
            siz[t1] += siz[t2];
        } else {
            no += (long) siz[rs[t1]] * siz[ls[t2]];
            yes += (long) siz[rs[t2]] * siz[ls[t1]];
            int mid = (l + r) >> 1;
            ls[t1] = merge(l, mid, ls[t1], ls[t2]);
            rs[t1] = merge(mid + 1, r, rs[t1], rs[t2]);
            up(t1);
        }
        return t1;
    }

    public static int dfs() throws IOException {
        int rt;
        int val = in.nextInt();
        if (val == 0) {
            int left = dfs();
            int right = dfs();
            no = yes = 0;
            rt = merge(1, n, left, right);
            ans += Math.min(no, yes);
        } else {
            rt = build(val, 1, n);
        }
        return rt;
    }

    public static FastReader in = new FastReader(System.in);

    public static PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));

    public static void main(String[] args) throws Exception {
        n = in.nextInt();
        dfs();
        out.println(ans);
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
