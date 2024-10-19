# Uniform Generator

## [Uniform Generator](http://poj.org/problem?id=1597)

> - ***Question***
>   - 如果有两个数字 `step` 和 `mod` ，那么可以由以下方式生成很多数字 `seed(1) = 0, seed(i + 1) = (seed(i) + step) % mod` ，比如 `step = 3, mod = 5, seed(1) = 0, seed(2) = 3, seed(3) = 1, seed(4) = 4, seed(5) = 2` 。如果能产生 `0 ~ mod - 1` 所有数字， `step` 和 `mod` 的组合叫 `"Good Choice"` ，如果无法产生 `0 ~ mod - 1` 所有数字， `step` 和 `mod` 的组合叫 `"Bad Choice"` 。根据 `step` 和 `mod` ，打印结果
>   - ***tips:***
>     - `1 <= step、mod <= 10^5`

---

## *Java*

> - ***裴蜀定理***

```java
import java.io.*;

public class Main {

    public static int gcd(int a, int b) {
        return b == 0 ? a : gcd(b, a % b);
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            int step = (int) in.nval;
            in.nextToken();
            int mod = (int) in.nval;
            out.print(String.format("%10d", step) + String.format("%10d", mod) + "    ");
            out.println(gcd(step, mod) == 1 ? "Good Choice" : "Bad Choice");
            out.println(" ");
        }
        out.flush();
        out.close();
        br.close();
    }

}
```

---

> ***last change: 2024/8/22***

---
