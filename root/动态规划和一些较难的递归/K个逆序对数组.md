# K个逆序对数组

## [629. K个逆序对数组](https://leetcode.cn/problems/k-inverse-pairs-array/)

> - ***Question***
>   - 给出两个整数 `n` 和 `k` ，找出所有包含从 `1` 到 `n` 的数字，且恰好拥有 `k` 个逆序对的不同的数组的个数。
>   - 逆序对的定义如下：对于数组的第 `i` 个和第 `j` 个元素，如果满足 `i < j` 且 `a[i] > a[j]` ，则其为一个逆序对；否则不是。
>   - 由于答案可能很大，只需要返回答案 `mod 109 + 7` 的值。
>   - ***tips:***
>     - `n` 的范围是 `[1, 1000]` 并且 `k` 的范围是 `[0, 1000]`

---

## *Java*

> - ***动态规划 + 斜率优化***

```java
class Solution {
    
    public int kInversePairs(int n, int k) {
        // dp[i][j] 表示所有包含0~i的数字且恰好拥有j个逆序对的不同的数组的个数
        int[][] dp = new int[n + 1][k + 1];
        // 第一行除了第一个位置什么都不选为1，其他都是0
        dp[0][0] = 1;
        int mod = 1000000007;
        // 从左往右，从上到下填表
        for (int i = 1; i < dp.length; i++) {
            // 0个逆序对只有严格自然序123456...i这一种
            dp[i][0] = 1;
            for (int j = 1; j < dp[0].length; j++) {
                // 根据j与i的关系有两种情况
                // 情况1：i>j
                // dp[5][3] = dp[4][0~3]
                // abcd5 -> abcd有3个
                // abc5d -> abcd有2个
                // ab5cd -> abcd有1个
                // a5bcd -> abcd有0个
                // 同理有dp[5][4] = dp[5][0~4]
                // 观察发现如下位置依赖，避免循环累计
                dp[i][j] = (dp[i - 1][j] + dp[i][j - 1]) % mod;
                // 情况2：i<=j
                // dp[7][9] = dp[6][3~9]
                // dp[7][10] = dp[6][4~10]
                // 有dp[i][j] = dp[i - 1][j] + dp[i][j - 1] - dp[i - 1][j - i]
                if (j >= i) {
                    // 避免原来dp[i][j]大于dp[i - 1][j - 1]，mod后反而小了导致负数的出现，先加上一个mod再取余
                    dp[i][j] = (dp[i][j] + mod - dp[i - 1][j - i]) % mod;
                }
            }
        }
        return dp[n][k];
    }
    
}
```

---

> ***last change: 2023/4/4***

---
