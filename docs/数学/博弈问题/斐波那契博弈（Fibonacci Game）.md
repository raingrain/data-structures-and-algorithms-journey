# 斐波那契博弈（Fibonacci Game）

## [P6487 [COCI2010-2011#4] HRPA](https://www.luogu.com.cn/problem/P6487)

> - **Question**
>   - 有 `n` 枚石子。两位玩家定了如下规则进行游戏：
>     - `Mirko` 先取一次， `Slavko` 再取一次，然后 `Mirko` 再取一次，两人轮流取石子，以此类推；
>     - `Mirko` 在第一次取石子时可以取走任意多个；
>     - 接下来，每次至少要取走一个石子，最多取走上一次取的数量的 `2` 倍。当然，玩家取走的数量必须不大于目前场上剩余的石子数量。
>     - 取走最后一块石子的玩家获胜。
>   - 双方都以最优策略取石子。 `Mirko` 想知道，自己第一次至少要取走几颗石子最终才能够获胜。
>   - **输入描述**
>     - 输入一行一个整数 `n` ，表示石子的数量。
>   - **输出描述**
>     - 输出一行一个整数，表示 `Mirko` 最少取多少石子可以保证获胜。
>   - **Tips**
>     - `2 <= n <= 10^15`

## Java

> - **Zeckendorf定理**
>   - 任何一个正整数都可以被拆成若干个不相邻的斐波纳契数之和。

```java
import java.io.*;

public class Main {

    public static long MAXN = 1000000000000000L;

    public static int MAXM = 101;

    public static long[] f = new long[MAXM];

    public static int size;

    public static void build() {
        f[0] = 1;
        f[1] = 2;
        size = 1;
        while (f[size] <= MAXN) {
            f[size + 1] = f[size] + f[size - 1];
            size++;
        }
    }

    public static void main(String[] args) throws IOException {
        build();
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            out.println(compute((long) in.nval));
        }
        out.flush();
        out.close();
        br.close();
    }

    public static long compute(long n) {
        long ans = -1, find;
        while (n != 1 && n != 2) {
            find = bs(n);
            if (n == find) {
                ans = find;
                break;
            } else {
                n -= find;
            }
        }
        if (ans != -1) {
            return ans;
        } else {
            return n;
        }
    }

    public static long bs(long n) {
        int l = 0;
        int r = size;
        int m;
        long ans = -1;
        while (l <= r) {
            m = (l + r) / 2;
            if (f[m] <= n) {
                ans = f[m];
                l = m + 1;
            } else {
                r = m - 1;
            }
        }
        return ans;
    }

}
```
