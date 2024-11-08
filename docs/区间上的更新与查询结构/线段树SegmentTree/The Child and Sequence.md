# The Child and Sequence

## [The Child and Sequence](https://www.luogu.com.cn/problem/CF438D)

> - **Question**
>   - 给定一个长度为 `n` 的数组 `arr` ，实现如下三种操作，一共调用 `m` 次：
>     - 操作 `1 l r` : 查询 `arr[l...r]` 的累加和；
>     - 操作 `2 l r x` : 把 `arr[l...r]` 上每个数字对 `x` 取模；
>     - 操作 `3 k x` : 把 `arr[k]` 上的数字设置为 `x` ；
>   - **Tips**
>     - `1 <= n, m <= 10^5`
>     - 操作 `1` 得到的结果，有可能超过 `int` 范围

## Java

> - **线段树 + 势能分析**

```java
// 一个数字v持续进行规模缩小的取模操作，次数上限是log v规模，因为值每次至少减一半
// 一段范围的每个数字进行取模操作之后，范围累加和信息并不能够快速得到
// 需要势能分析来评估复杂度，还有剪枝的重要性，一般的剪枝只是优化常数时间，这里要重要的多
// 注意到操作3又会让势能增加，不过好在是单点操作，势能增加很有限，势能单次增加(logv * logn)
import java.io.*;

public class Main {

    public static int MAXN = 100001;

    public static int[] arr = new int[MAXN];

    public static long[] sum = new long[MAXN << 2];

    public static int[] max = new int[MAXN << 2];

    public static void up(int i) {
        sum[i] = sum[i << 1] + sum[i << 1 | 1];
        max[i] = Math.max(max[i << 1], max[i << 1 | 1]);
    }

    public static void build(int l, int r, int i) {
        if (l == r) {
            sum[i] = max[i] = arr[l];
        } else {
            int mid = (l + r) >> 1;
            build(l, mid, i << 1);
            build(mid + 1, r, i << 1 | 1);
            up(i);
        }
    }

    public static long query(int jobl, int jobr, int l, int r, int i) {
        if (jobl <= l && r <= jobr) {
            return sum[i];
        }
        int mid = (l + r) >> 1;
        long ans = 0;
        if (jobl <= mid) {
            ans += query(jobl, jobr, l, mid, i << 1);
        }
        if (jobr > mid) {
            ans += query(jobl, jobr, mid + 1, r, i << 1 | 1);
        }
        return ans;
    }

    public static void mod(int jobl, int jobr, int jobv, int l, int r, int i) {
        if (jobv > max[i]) {
            return;
        }
        if (l == r) {
            sum[i] %= jobv;
            max[i] %= jobv;
        } else {
            int mid = (l + r) >> 2;
            if (jobl <= mid) {
                mod(jobl, jobr, jobv, l, mid, i << 1);
            }
            if (jobr > mid) {
                mod(jobl, jobr, jobv, mid + 1, r, i << 1 | 1);
            }
            up(i);
        }
    }

    public static void update(int jobi, int jobv, int l, int r, int i) {
        if (l == r) {
            sum[i] = max[i] = jobv;
        } else {
            int mid = (l + r) >> 1;
            if (jobi <= mid) {
                update(jobi, jobv, l, mid, i << 1);
            } else {
                update(jobi, jobv, mid + 1, r, i << 1 | 1);
            }
            up(i);
        }
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
            arr[i] = (int) in.nval;
        }
        build(1, n, 1);
        for (int i = 1, op; i <= m; i++) {
            in.nextToken();
            op = (int) in.nval;
            if (op == 1) {
                in.nextToken();
                int jobl = (int) in.nval;
                in.nextToken();
                int jobr = (int) in.nval;
                out.println(query(jobl, jobr, 1, n, 1));
            } else if (op == 2) {
                in.nextToken();
                int jobl = (int) in.nval;
                in.nextToken();
                int jobr = (int) in.nval;
                in.nextToken();
                int jobv = (int) in.nval;
                mod(jobl, jobr, jobv, 1, n, 1);
            } else {
                in.nextToken();
                int jobi = (int) in.nval;
                in.nextToken();
                int jobv = (int) in.nval;
                update(jobi, jobv, 1, n, 1);
            }
        }
        out.flush();
        out.close();
        br.close();
    }

}
```
