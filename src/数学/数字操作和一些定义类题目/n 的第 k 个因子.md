# n 的第 k 个因子

## [1492. n 的第 k 个因子](https://leetcode.cn/problems/the-kth-factor-of-n/)

> - ***Question***
>   - 给你两个正整数 `n` 和 `k` 。
>   - 如果正整数 `i` 满足 `n % i == 0` ，那么我们就说正整数 `i` 是整数 `n` 的因子。
>   - 考虑整数 `n` 的所有因子，将它们升序排列。请你返回第 `k` 个因子。如果 `n` 的因子数少于 `k` ，请你返回 `-1` 。
>   - ***tips:***
>     - `1 <= k <= n <= 1000`

---

## *Java*

> - ***遍历***
>   - 我们可以从小到大枚举所有在 `[1, sqrt(n)]` 范围内的数，并判断是否为 `n` 的因子。

```java
// 如果n是完全平方数，那么满足x^2=n的因子x被枚举了两次，需要忽略其中的一次。
class Solution {

    public int kthFactor(int n, int k) {
        int count = 0, factor;
        for (factor = 1; factor * factor <= n; ++factor) {
            if (n % factor == 0) {
                ++count;
                if (count == k) {
                    return factor;
                }
            }
        }
        --factor;
        if (factor * factor == n) {
            --factor;
        }
        for (; factor > 0; --factor) {
            if (n % factor == 0) {
                ++count;
                if (count == k) {
                    return n / factor;
                }
            }
        }
        return -1;
    }

}
```
