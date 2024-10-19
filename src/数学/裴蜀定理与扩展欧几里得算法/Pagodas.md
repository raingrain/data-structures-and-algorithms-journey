# Pagodas

## [Pagodas](https://acm.hdu.edu.cn/showproblem.php?pid=5512)

> - ***Question***
>   - 一共有编号 `1 ~ n` 的宝塔，其中 `a` 号和 `b` 号宝塔已经修好了，Yuwgna和Iaka两个人轮流修塔，Yuwgna先手，Iaka后手，谁先修完所有的塔谁赢，每次可以选择 `j + k` 号或者 `j - k` 号塔进行修理，其中j和k是任意两个已经修好的塔，也就是输入 `n, a, b` ，如果先手赢打印 `Yuwgna` ，后手赢打印 `Iaka` 。
>   - ***tips:***
>     - `2 <= n <= 2 * 10^4`

---

## *Java*

> - ***分类讨论***

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
        in.nextToken();
        int cases = (int) in.nval;
        for (int t = 1, n, a, b; t <= cases; t++) {
            in.nextToken();
            n = (int) in.nval;
            in.nextToken();
            a = (int) in.nval;
            in.nextToken();
            b = (int) in.nval;
            out.print("Case #" + t + ": ");
            if (((n / gcd(a, b)) & 1) == 1) {
                out.println("Yuwgna");
            } else {
                out.println("Iaka");
            }
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
