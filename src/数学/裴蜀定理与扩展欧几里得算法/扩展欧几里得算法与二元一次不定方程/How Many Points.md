# How Many Points

## [How Many Points?](https://lightoj.com/problem/how-many-points)

> - ***Question***
>   - 二维网格中只有 `x` 和 `y` 的值都为整数的坐标，才叫格点，给定两个格点 `A` 在 `(x1, y1)` 而 `B` 在 `(x2, y2)` ，返回 `A` 和 `B` 的连线上，包括 `A` 和 `B` 在内，一共有几个格点。
>   - ***tips:***
>     - `-10^9 <= x1、y1、x2、y2 <= 10^9`

## Java

> - ***Question***

```java
import java.io.*;

public class Main {

    public static long gcd(long a, long b) {
        return b == 0 ? a : gcd(b, a % b);
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        int cases = (int) in.nval;
        for (int t = 1; t <= cases; t++) {
            in.nextToken();
            long x1 = (long) in.nval;
            in.nextToken();
            long y1 = (long) in.nval;
            in.nextToken();
            long x2 = (long) in.nval;
            in.nextToken();
            long y2 = (long) in.nval;
            long ans = gcd(Math.abs(x1 - x2), Math.abs(y1 - y2)) + 1;
            out.println("Case " + t + ": " + ans);
        }
        out.flush();
        out.close();
        br.close();
    }

}
```
