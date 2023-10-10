# 第 N 个神奇数字

## [878. 第 N 个神奇数字](https://leetcode.cn/problems/nth-magical-number/)

> - ***Question***
>   - 一个正整数如果能被 `a` 或 `b` 整除，那么它是神奇的。
>   - 给定三个整数 `n, a, b` ，返回第 `n` 个神奇的数字。因为答案可能很大，所以返回答案对 `10 ^ 9 + 7` 取模后的值。
>   - ***tips:***
>     - `1 <= n <= 10 ^ 9`
>     - `2 <= a, b <= 4 * 10 ^ 4`

---

## *Java*

> - ***二分答案法 + gcd + lcm***

```java
class Solution {

    // 答案一定在(1, a/n)之间
    // b的加入一定会使范围缩小
    public int nthMagicalNumber(int n, int a, int b) {
        long lcm = lcm(a, b);
        long ans = 0;
        // l = 0
        // r = (long) n * Math.min(a, b)
        // l......r
        for (long l = 0, r = (long) n * Math.min(a, b), m = 0; l <= r; ) {
            m = (l + r) / 2;
            // 1....m
            // 容斥原理，1~n上有几个能被a或b整除的数字
            // m/a 有几个能被a整除
            // m/b 有几个能被b整除
            // m/lcm(a,b)重复的
            if (m / a + m / b - m / lcm >= n) {
                // 存在，看左边有没有更早出现的
                ans = m;
                r = m - 1;
            } else {
                // 不够，往右查询
                l = m + 1;
            }
        }
        return (int) (ans % 1000000007);
    }

    private long gcd(long a, long b) {
        return b == 0 ? a : gcd(b, a % b);
    }

    // 最小公倍数
    private long lcm(long a, long b) {
        return a / gcd(a, b) * b;
    }

}
```

---

> ***last change: 2023/10/10***

---
