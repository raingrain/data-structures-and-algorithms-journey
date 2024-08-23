# 扩展欧几里得算法与二元一次不定方程

![image](./images/扩展欧几里得算法与二元一次不定方程.png)

## [P5656 【模板】二元一次不定方程 (exgcd)](https://www.luogu.com.cn/problem/P5656)

> - ***Question***
>   - 给定 `a, b, c` ，求解方程 `ax + by = c` ，如果方程无解打印 `-1` ，如果方程无正整数解，但是有整数解，打印这些整数解中， `x` 的最小正数值， `y` 的最小正数值，如果方程有正整数解，打印正整数解的数量，同时打印所有正整数解中， `x` 的最小正数值， `y` 的最小正数值， `x` 的最大正数值， `y` 的最大正数值。
>   - ***tips:***
>     - `1 <= a、b、c <= 10^9`

---

## *Java*

> - ***Question: 模板***

```java
import java.io.*;

public class Main {

    // 扩展欧几里得算法
    public static long d, x, y, px, py;

    public static void exgcd(long a, long b) {
        if (b == 0) {
            d = a;
            x = 1;
            y = 0;
        } else {
            exgcd(b, a % b);
            px = x;
            py = y;
            x = py;
            y = px - py * (a / b);
        }
    }

    public static long a, b, c, xd, yd, times;

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        int cases = (int) in.nval;
        for (int t = 1; t <= cases; t++) {
            in.nextToken();
            a = (long) in.nval;
            in.nextToken();
            b = (long) in.nval;
            in.nextToken();
            c = (long) in.nval;
            exgcd(a, b);
            if (c % d != 0) { // 无整数解
                out.println(-1);
            } else { // 有整数解
                x *= c / d;
                y *= c / d;
                xd = b / d;
                yd = a / d;
                if (x < 0) {
                    // x要想增长到>=1且最小的值，差几个xd，算出来就是k的值
                    // 那应该是(1-x)/xd，结果向上取整
                    times = (1 - x + xd - 1) / xd;
                    x += xd * times;
                    y -= yd * times;
                } else {
                    // x要想减少到>=1且最小的值，差几个xd，算出来就是k的值，向下取整
                    times = (x - 1) / xd;
                    x -= xd * times;
                    y += yd * times;
                }
                // 此时得到的(x, y)，是x为最小正整数时的一组解
                // 然后继续讨论
                if (y <= 0) { // 无正整数解
                    // x能取得的最小正数
                    out.print(x + " ");
                    // y能取得的最小正数
                    out.println(y + yd * ((1 - y + yd - 1) / yd));
                } else { // 有正整数解
                    // y减少到1以下，能减几次，就是正整数解的个数
                    out.print(((y - 1) / yd + 1) + " ");
                    // x能取得的最小正数
                    out.print(x + " ");
                    // y能取得的最小正数
                    out.print((y - (y - 1) / yd * yd) + " ");
                    // x能取得的最大正数
                    out.print((x + (y - 1) / yd * xd) + " ");
                    // y能取得的最大正数
                    out.println(y);
                }
            }
        }
        out.flush();
        out.close();
        br.close();
    }

}
```

---

> ***last change: 2024/8/23***

---
