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

## Java

> - ***Question :裴蜀定理***

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

> - ***扩展欧几里得算法***

```java
public class Main {

    // 扩展欧几里得算法
    // 对于方程ax + by = gcd(a,b)
    // 当a和b确定，那么gcd(a,b)也确定
    // 扩展欧几里得算法可以给出a和b的最大公约数d、以及其中一个特解x、y
    // 特别注意要保证入参a和b没有负数
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

    // 费马小定理计算逆元
    public static long fermat(long num, long mod) {
        return power(num, mod - 2, mod);
    }

    public static long power(long num, long pow, long mod) {
        long ans = 1;
        while (pow > 0) {
            if ((pow & 1) == 1) {
                ans = (ans * num) % mod;
            }
            num = (num * num) % mod;
            pow >>= 1;
        }
        return ans;
    }

    public static void main(String[] args) {
        // 扩展欧几里得算法例子
        int a = 220;
        int b = 170;
        exgcd(a, b);
        System.out.println("gcd(" + a + ", " + b + ")" + " = " + d);
        System.out.println("x = " + x + ", " + " y = " + y);

        // 扩展欧几里得算法可以去求逆元
        System.out.println("求逆元测试开始");
        long mod = 1000000007;
        long test = 10000000;
        for (long num = 1; num <= test; num++) {
            exgcd(num, mod);
            x = (x % mod + mod) % mod;
            if (x != fermat(num, mod)) {
                System.out.println("出错了!");
            }
        }
        System.out.println("求逆元测试结束");
    }

}
```
