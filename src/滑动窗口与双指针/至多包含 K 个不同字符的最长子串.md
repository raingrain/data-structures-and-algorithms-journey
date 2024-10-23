# 至多包含k个不同字符的最长子串

## [159. 至多包含两个不同字符的](https://leetcode.cn/problems/longest-substring-with-at-most-two-distinct-characters/)

## [340. 至多包含 K 个不同字符的最长子串](https://leetcode.cn/problems/longest-substring-with-at-most-k-distinct-characters/)

> - **Question**
>   - 给定一个字符串和一个限定数 `k` ，请你返回最多包含 `k` 种不同字符的最长子串的长度。

## Java

> - **滑动窗口**

```java
class Solution {
    
    public int lengthOfLongestSubstringKDistinct(String s, int k) {
        if (s == null || s.length() == 0 || k < 1) {
            return 0;
        }
        char[] str = s.toCharArray();
        // 窗口内词频表
        int[] count = new int[256];
        // 窗口内有多少种字符
        int kind = 0;
        // 左闭右开右边界
        int right = 0;
        int ans = 0;
        for (int i = 0; i < str.length; i++) {
            // right 窗口的右边界
            // 不越界，且种类没有超过k或者等于k但新的字符滑动窗口里面有
            while (right < str.length && (kind < k || (kind == k && count[str[right]] > 0))) {
                kind += count[str[right]] == 0 ? 1 : 0;
                count[str[right++]]++;
            }
            // right来到违规的第一个位置
            ans = Math.max(ans, right - i);
            // 如果左边界字符不是只出现一次，那就说明窗口里面有这个字符，词频减1就行，种类不减，否则减
            kind -= count[str[i]] == 1 ? 1 : 0;
            count[str[i]]--;
        }
        return ans;
    }
    
}
```
