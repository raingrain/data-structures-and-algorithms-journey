# [剑指 Offer 46. 把数字翻译成字符串](https://leetcode.cn/problems/ba-shu-zi-fan-yi-cheng-zi-fu-chuan-lcof/)

### 解题思路
1. 具体思路看代码

### 代码

```java
// java暴力递归转动态规划
class Solution {
    public int translateNum(int num) {
        // 动态规划
        return translateNumByDP(String.valueOf(num).toCharArray());
        // 暴力递归
        // return translateNumByRecursion(String.valueOf(num).toCharArray(), 0);
    }

    public int translateNumByDP(char[] str) {
        int len = str.length;
        int[] dp = new int[len + 1];
        // 递归的终止条件
        dp[len] = 1;
        for (int index = len - 1; index >= 0; index--) {
            // 搬过来直接抄，函数改成dp即可
            dp[index] = dp[index + 1];
            if (index + 1 < len && str[index] != '0' && (str[index] - '0') * 10 + str[index + 1] - '0' < 26) {
                dp[index] += dp[index + 2];
            }
        }
        return dp[0];
    }

    // 暴力递归
    // 可变参数只有index
    // 缓存表是一维的
    // 假定[0-index]位置的情况我已经搞定了，我只需要考虑[index-len]的情况
    public int translateNumByRecursion(char[] str, int index) {
        // 某条路径走完了，一种可以转换的方法被找到
        if (index == str.length) {
            return 1;
        } else {
            // 每一个位置上的数字一定有单独转成一个字母的情况
            int ans = translateNumByRecursion(str, index + 1);
            // 当我有下一个数字， 并且当前数字与下一个数字的组合不是‘03’形式，且组成的两位数小于26时（因为题目说25是z）
            // 字符相减也就是ASCII码相减，26个字母被0-25划分
            if (index + 1 < str.length && str[index] != '0' && (str[index] - '0') * 10 + str[index + 1] - '0' < 26) {
                // 直接跳到下下一个数字
                ans += translateNumByRecursion(str, index + 2);
            }
            return ans;
        }
    }
}
```

