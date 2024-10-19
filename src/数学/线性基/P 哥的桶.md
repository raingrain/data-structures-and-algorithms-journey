# P 哥的桶

## [P4839 P 哥的桶](https://www.luogu.com.cn/problem/P4839)

> - ***Question***
>   - P哥现在有 `n` 个桶，它们排成了一排，这些桶可以装下任意多个球。每个球有一个固定的价值。
>   - P哥时不时地会找新球，并把新找的球丢进某个桶里面。我们用 `1 k x` 来表示P哥找了一个价值为 `x` 的球，并且丢进了 `k` 号桶里面。
>   - P哥每次会在特定的桶里面拿出一些球。我们用 `2 l r` 来表示P哥在 `l` 号桶到 `r` 号桶之间拿球。P哥希望拿出来的球的价值异或和尽可能大。
>   - ***输入描述***
>     - 第一行两个整数 `n, m` ，依次表示P哥的操作次数、这组数据会涉及到的最大编号。接下来 `n` 行，每行三个整数，表示操作。操作格式如题。
>   - ***输出描述***
>     - 对于每个 `2` 操作，输出P哥拿出的球的最大价值异或和。
>   - ***tips:***
>     - `1 <= n, m <= 5 * 10^4`
>     - `1 <= k <= m`
>     - `0 <= x <= 2^31 - 1`
>     - `1 <= l <= r <= m`

---

## *Java*

> - ***异或空间线性基 + 线段树***

```java
import java.io.*;
import java.util.Arrays;

public class Main {

    public static int MAXN = 50001;

    public static int BIT = 30;

    // 线段树的每个范围上维护线性基
    public static int[][] treeBasis = new int[MAXN << 2][BIT + 1];

    public static int[] basis = new int[BIT + 1];

    public static void add(int jobi, int jobv, int l, int r, int i) {
        insert(treeBasis[i], jobv);
        if (l < r) {
            int mid = (l + r) >> 1;
            if (jobi <= mid) {
                add(jobi, jobv, l, mid, i << 1);
            } else {
                add(jobi, jobv, mid + 1, r, i << 1 | 1);
            }
        }
    }

    public static boolean insert(int[] basis, int num) {
        for (int i = BIT; i >= 0; i--) {
            if (num >> i == 1) {
                if (basis[i] == 0) {
                    basis[i] = num;
                    return true;
                }
                num ^= basis[i];
            }
        }
        return false;
    }

    public static int query(int jobl, int jobr, int m) {
        Arrays.fill(basis, 0);
        merge(jobl, jobr, 1, m, 1);
        int ans = 0;
        for (int j = BIT; j >= 0; j--) {
            ans = Math.max(ans, ans ^ basis[j]);
        }
        return ans;
    }

    public static void merge(int jobl, int jobr, int l, int r, int i) {
        if (jobl <= l && r <= jobr) {
            for (int j = BIT; j >= 0; j--) {
                if (treeBasis[i][j] != 0) {
                    insert(basis, treeBasis[i][j]);
                }
            }
        } else {
            int mid = (l + r) >> 1;
            if (jobl <= mid) {
                merge(jobl, jobr, l, mid, i << 1);
            }
            if (jobr > mid) {
                merge(jobl, jobr, mid + 1, r, i << 1 | 1);
            }
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
            int op = (int) in.nval;
            if (op == 1) {
                in.nextToken();
                int jobi = (int) in.nval;
                in.nextToken();
                int jobv = (int) in.nval;
                add(jobi, jobv, 1, m, 1);
            } else {
                in.nextToken();
                int jobl = (int) in.nval;
                in.nextToken();
                int jobr = (int) in.nval;
                out.println(query(jobl, jobr, m));
            }
        }
        out.flush();
        out.close();
        br.close();
    }

}
```

---

> ***last change: 2024/7/31***

---
