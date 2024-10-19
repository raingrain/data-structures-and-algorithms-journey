# 重复的DNA序列

## [187. 重复的DNA序列](https://leetcode.cn/problems/repeated-dna-sequences/)

> - ***Question***
>   - `DNA` 序列由一系列核苷酸组成，缩写为 `'A', 'C', 'G', 'T'`。给定一个表示 `DNA` 序列的字符串 `s` ，返回所有在 `DNA` 分子中出现不止一次的长度为 `10` 的序列（子字符串）。你可以按任意顺序返回答案。
>   - ***tips:***
>     - `0 <= s.length <= 10^5`
>     - `s[i]=='A'、'C'、'G' or 'T'`

---

## *Java*

> - ***哈希表***
>   - 我们可以用一个哈希表统计 `s` 所有长度为 `10` 的子串的出现次数，返回所有出现次数超过 `10` 的子串。代码实现时，可以一边遍历子串一边记录答案，为了不重复记录答案，我们只统计当前出现次数为 `2` 的子串。

```java
import java.util.*;

class Solution {

    static final int L = 10;

    public List<String> findRepeatedDnaSequences(String s) {
        ArrayList<String> ans = new ArrayList<>();
        HashMap<String, Integer> cnt = new HashMap<>();
        int n = s.length();
        for (int i = 0; i <= n - L; ++i) {
            String sub = s.substring(i, i + L);
            cnt.put(sub, cnt.getOrDefault(sub, 0) + 1);
            if (cnt.get(sub) == 2) {
                ans.add(sub);
            }
        }
        return ans;
    }

}
```
