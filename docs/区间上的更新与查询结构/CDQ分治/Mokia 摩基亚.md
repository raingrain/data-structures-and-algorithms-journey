# Mokia 摩基亚

## [P4390 [BalkanOI 2007] Mokia 摩基亚](https://www.luogu.com.cn/problem/P4390)

> - **Question**
>   - 给定数字 `w` ，表示一个 `w * w` 的正方形区域，所有位置都在其中，接下来有 `m` 条操作，每种操作是如下两种类型中的一种：
>     - 操作 `1 x y v`   : 坐标 `(x, y)` 位置增加了 `v` 个人。
>     - 操作 `2 a b c d` : 打印左上角 `(a, b)` 、右下角 `(c, d)` 区域里的人数。
>   - **Tips**
>     - `1 <= w <= 2 * 10^6`
>     - `1 <= m <= 2 * 10^5`
>     - `0 <= v <= 10^4`

## Java

> - **Question**

```java
// 操作依次发生，对应每个事件有时序
// 大顺序根据时序组织，CDQ分治时按照x值排序，某个y值对应的v，用树状数组统计
// 注意不要让树状数组下标出现0！

// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXM = 200001;
    public static int MAXV = 2000002;
    public static int w;

    // op == 1表示增加事件，x、y、人数v
    // op == 2表示查询事件，x、y、效果v、查询编号q
    public static int[][] arr = new int[MAXM][5];
    public static int cnte = 0;
    public static int cntq = 0;

    // 树状数组
    public static int[] tree = new int[MAXV];

    public static int[] ans = new int[MAXM];

    public static int lowbit(int i) {
        return i & -i;
    }

    public static void add(int i, int v) {
        while (i <= w) {
            tree[i] += v;
            i += lowbit(i);
        }
    }

    public static int query(int i) {
        int ret = 0;
        while (i > 0) {
            ret += tree[i];
            i -= lowbit(i);
        }
        return ret;
    }

    public static void addPeople(int x, int y, int v) {
        arr[++cnte][0] = 1;
        arr[cnte][1] = x;
        arr[cnte][2] = y;
        arr[cnte][3] = v;
    }

    public static void addQuery(int x, int y, int v, int q) {
        arr[++cnte][0] = 2;
        arr[cnte][1] = x;
        arr[cnte][2] = y;
        arr[cnte][3] = v;
        arr[cnte][4] = q;
    }

    public static void merge(int l, int m, int r) {
        int p1, p2;
        for (p1 = l - 1, p2 = m + 1; p2 <= r; p2++) {
            while (p1 + 1 <= m && arr[p1 + 1][1] <= arr[p2][1]) {
                p1++;
                if (arr[p1][0] == 1) {
                    add(arr[p1][2], arr[p1][3]);
                }
            }
            if (arr[p2][0] == 2) {
                ans[arr[p2][4]] += arr[p2][3] * query(arr[p2][2]);
            }
        }
        for (int i = l; i <= p1; i++) {
            if (arr[i][0] == 1) {
                add(arr[i][2], -arr[i][3]);
            }
        }
        Arrays.sort(arr, l, r + 1, (a, b) -> a[1] - b[1]);
    }

    public static void cdq(int l, int r) {
        if (l == r) {
            return;
        }
        int mid = (l + r) / 2;
        cdq(l, mid);
        cdq(mid + 1, r);
        merge(l, mid, r);
    }

    public static void main(String[] args) throws IOException {
        FastReader in = new FastReader(System.in);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextInt();
        w = in.nextInt() + 1;
        int op, x, y, v, a, b, c, d;
        op = in.nextInt();
        while (op != 3) {
            if (op == 1) {
                x = in.nextInt() + 1;
                y = in.nextInt() + 1;
                v = in.nextInt();
                addPeople(x, y, v);
            } else {
                a = in.nextInt() + 1;
                b = in.nextInt() + 1;
                c = in.nextInt() + 1;
                d = in.nextInt() + 1;
                addQuery(c, d, 1, ++cntq);
                addQuery(a - 1, b - 1, 1, cntq);
                addQuery(a - 1, d, -1, cntq);
                addQuery(c, b - 1, -1, cntq);
            }
            op = in.nextInt();
        }
        cdq(1, cnte);
        for (int i = 1; i <= cntq; i++) {
            out.println(ans[i]);
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
