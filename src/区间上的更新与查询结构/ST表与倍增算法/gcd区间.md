# gcd区间

## [P1890 gcd区间](https://www.luogu.com.cn/problem/P1890)

> - ***Question***
>   - 给定 `n` 个正整数 `a1, a2, ..., an` 。
>   - `m` 次询问，每次询问给定一个区间 `[l, r]`，输出 `al, al+1, ..., ar` 的最大公因数。
>   - ***输入描述***
>     - 第一行两个整数 `n, m` 。
>     - 第二行 `n` 个整数表示 `a1, a2, ..., an` 。
>     - 以下 `m` 行，每行两个整数 `l, r` 表示询问区间的左右端点。
>   - ***输出描述***
>     - 共 `m` 行，每行表示一个询问的答案。
>   - ***tips:***
>     - `1 <= l <= r <= n <= 1000`
>     - `1 <= m <= 10^6`
>     - `1 <= ai <= 10^9`

---

## *Java*

> - ***ST表***

```java
import java.io.*;

public class Main {

    public static int MAXN = 1001;

    public static int LIMIT = 10;

    public static int[] arr = new int[MAXN];

    public static int[] log2 = new int[MAXN];

    public static int[][] stgcd = new int[MAXN][LIMIT];

    public static void build(int n) {
        log2[0] = -1;
        for (int i = 1; i <= n; i++) {
            log2[i] = log2[i >> 1] + 1;
            stgcd[i][0] = arr[i];
        }
        for (int p = 1; p <= log2[n]; p++) {
            for (int i = 1; i + (1 << p) - 1 <= n; i++) {
                stgcd[i][p] = gcd(stgcd[i][p - 1], stgcd[i + (1 << (p - 1))][p - 1]);
            }
        }
    }

    // 算法讲解041 - 辗转相除法求最大公约数
    public static int gcd(int a, int b) {
        return b == 0 ? a : gcd(b, a % b);
    }

    public static int query(int l, int r) {
        int p = log2[r - l + 1];
        return gcd(stgcd[l][p], stgcd[r - (1 << p) + 1][p]);
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
        build(n);
        for (int i = 1, l, r; i <= m; i++) {
            in.nextToken();
            l = (int) in.nval;
            in.nextToken();
            r = (int) in.nval;
            out.println(query(l, r));
        }
        out.flush();
        out.close();
        br.close();
    }

}
```

---

> ***last change: 2024/4/26***

---
