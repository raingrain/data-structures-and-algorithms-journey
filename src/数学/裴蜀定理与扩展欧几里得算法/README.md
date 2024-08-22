# 裴蜀定理与扩展欧几里得算法

## 裴蜀定理

如果 `a` 和 `b` 是不全为 `0` 的整数，则有整数 `x, y` ，使得 `ax + by = gcd(a, b)` 。

`a` 和 `b` 的最大公约数等于 `ax` + `by` （随意给定整数 `x, y` ，能得到的最小正数差值）。

## 裴蜀定理推论

1. 如果 `a` 和 `b` 是不全为 `0` 的整数， `a` 和 `b` 互质，当且仅当存在整数 `x, y` ，使 `ax + by = 1` 。
2. 如果 `a` 和 `b` 是不全为 `0` 的整数，并且 `ax + by = c` 有解，那么 `c` 一定是 `gcd(a, b)` 的整数倍。
3. a和b两项的裴蜀定理，可以推广到多项的情况。
4. 如果 `ax + by = c` 一旦有解，就意味着一定有无穷多组 `(x, y)` ，都可以使得式子成立。

## 扩展欧几里得算法

对于方程 `ax + by = gcd(a, b)` ，当 `a` 和 `b` 确定，那么 `gcd(a, b)` 也确定，要保证入参 `a` 和 `b` 没有负数。

扩展欧几里得算法可以给出 `a` 和 `b` 的最大公约数 `d` 、以及其中一个特解 `x, y` 。

扩展欧几里得算法理论的时间复杂度 `O(log min{a, b})` ，但要注意 `%` 运算的代价比较高，
所以实际的时间复杂度 `O((log min{a,b})^3)` 。

费马小定理求逆元时，要求模的数字必须为质数。

扩展欧几里得算法求逆元时，不要求模的数字必须为质数，但要求 `a` 和 `b` 互质。

## [P4549 【模板】裴蜀定理](https://www.luogu.com.cn/problem/P4549)

> - ***Question***
>   - 给定长度为 `n` 的一组整数值 `[a1, a2, a3...]` ，你找到一组数值 `[x1, x2, x3...]` ，要让 `a1*x1 + a2*x2 + a3*x3 + ...` 得到的结果为最小正整数，返回能得到的最小正整数是多少。
>   - ***tips:***
>     - `1 <= n <= 20`
>     - `1 <= ai <= 10^5`

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
        in.nextToken();
        int n = (int) in.nval;
        int ans = 0;
        for (int i = 1; i <= n; i++) {
            in.nextToken();
            ans = gcd(Math.abs((int) in.nval), ans);
        }
        out.println(ans);
        out.flush();
        out.close();
        br.close();
    }

}
```

---

> ***last change: 2024/8/22***

---
