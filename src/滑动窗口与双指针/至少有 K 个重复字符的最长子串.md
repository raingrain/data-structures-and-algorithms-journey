# 至少有 K 个重复字符的最长子串

## [395. 至少有 K 个重复字符的最长子串](https://leetcode.cn/problems/longest-substring-with-at-least-k-repeating-characters/)

> - ***Question***
>   - 给你一个字符串 `s` 和一个整数 `k` ，请你找出 `s` 中的最长子串，要求该子串中的每一字符出现次数都不少于 `k` 。返回这一子串的长度。
>   - ***tips:***
>     - `1 <= s.length <= 10^4`
>     - `s` 仅由小写英文字母组成
>     - `1 <= k <= 10^5`

---

## *Java*

> - ***滑动窗口***
>   - 我们枚举最长子串中的字符种类数目，对于给定的字符种类数目，我们求解 `s` 中的最长子串，该子串中的每一字符出现次数都不少于 `k` ，且只能有限定的字符种类。那么答案必在其中。
>   - 对于每一个字符种类下的求解，使用滑动窗口，因为在限定了字符种类后出现了单调性。

```java
class Solution {
    
    public int longestSubstring(String s, int k) {
        char[] str = s.toCharArray();
        int ans = 0;
        for (int require = 1; require <= 26; require++) {
            // a~z 出现次数
            int[] count = new int[26];
            // 目前窗口内收集了几种字符了
            int collect = 0;
            // 目前窗口内出现次数>=k次的字符，满足了几种
            int satisfy = 0;
            // 窗口右边界
            int right = -1;
            // left要尝试每一个窗口的最左位置
            for (int left = 0; left < str.length; left++) {
                // [left..right] right+1
                // 下一个字符收集金量不会是我其中的字符种类变多
                // 每个窗口内的字符种类是固定的，不满足要求要结算
                while (right + 1 < str.length && !(collect == require && count[str[right + 1] - 'a'] == 0)) {
                    right++;
                    if (count[str[right] - 'a'] == 0) {
                        // 新字符
                        collect++;
                    }
                    if (count[str[right] - 'a'] == k - 1) {
                        // 字符出现次数大于等于k了
                        satisfy++;
                    }
                    // 词频加一
                    count[str[right] - 'a']++;
                }
                // [left...right]
                // 一种答案
                if (satisfy == require) {
                    ans = Math.max(ans, right - left + 1);
                }
                // left++
                if (count[str[left] - 'a'] == 1) {
                    // 只出现一次
                    collect--;
                }
                if (count[str[left] - 'a'] == k) {
                    // 出现刚好是k次，下一个滑动窗口就不能满足题目了
                    satisfy--;
                }
                count[str[left] - 'a']--;
            }
        }
        return ans;
    }
    
}
```

---

> ***last change: 2023/4/20***

---
