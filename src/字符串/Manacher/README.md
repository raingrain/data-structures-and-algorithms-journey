# Manacher算法代码模板

## [5. 最长回文子串](https://leetcode.cn/problems/longest-palindromic-substring/)

> - ***Question 1***
>   - 给你一个字符串 `s` ，找到 `s` 中最长的回文子串长度。
> - ***Question 2***
>   - 给你一个字符串 `s` ，找到 `s` 中最长的回文子串。

---

## *Java*

> - ***Manacher大概思路***
>   - 先把给定的字符串 `s` 转化成 `manacher` 字符数组，遍历 `manacher` 字符数组， 求出每一个位置的回文半径，回文半径最大值即为最长回文子串长度，对应的回文串即为最长的回文子串。
>   - 利用最右回文半径索引和最右回文半径中心点，看当前遍历到的位置关于该最右回文半径中心点的对称点的回文区域与最左回文半径（当前最右回文半径关于中心点的对称点）的情况：
>     - 被囊括，当前位置回文区域和对称点相同。
>     - 压住或者大于最左回文半径，当前位置的回文半径最少是最左回文半径到对称点的距离即最右回文半径到当前位置的距离。
>     - 在最右回文半径的右侧，回文区域至少是 `1` 。
>     - 至少情况的都要往两边扩张看回文区域还能不能再变大，如果能还要更新最右回文半径和对应中心点。
>   - 记录回文半径数组或者最大值即可得到子串的回文情况，回文半径减一就是原串长度。
>   - 不给出证明。

```java
class Solution {
    
    // 获取最长回文子串
    public String longestPalindrome(String s) {
        // if (s == null || s.length() == 0) {
        //     return null;
        if (s.length() == 1) {
            return s;
        } else if (s.length() == 2) {
            // String.charAt(i)提取String的第i个字符，String.valueOf()将一个字符转为字符串
            return s.charAt(0) == s.charAt(1) ? s : String.valueOf(s.charAt(1));
        } else {
            return manacher(s);
        }
    }
    
    public String manacher(String s) {
        char[] str = getManacherString(s);
        int[] radius = new int[str.length];
        int right = -1;
        int mid = -1;
        // 当前最大回文半径对应的回文中心点，如果有多个最大回文半径，记录最早发现的那一个
        int maxMid = -1;
        int max = Integer.MIN_VALUE;
        int i = 0;
        for (; i < str.length; i++) {
            radius[i] = right > i ? Math.min(radius[2 * mid - i], right - i) : 1;
            while (i + radius[i] < str.length && i - radius[i] >= 0) {
                if (str[i + radius[i]] == str[i - radius[i]]) {
                    radius[i]++;
                } else {
                    break;
                }
            }
            if (i + radius[i] > right) {
                right = i + radius[i];
                mid = i;
            }
            // 更新一下最右回文半径和中心点
            if (radius[i] > max) {
                max = radius[i];
                maxMid = i;
            }
        }
        // 在manacher串中找出对应最长回文串，去除#拼接后就是要求的
        StringBuilder res = new StringBuilder();
        for (int j = maxMid - max + 1; j < maxMid + max; j++) {
            if (str[j] != '#') {
                res.append(str[j]);
            }
        }
        return res.toString();
    }
    
    public char[] getManacherString(String str) {
        char[] charArray = str.toCharArray();
        char[] res = new char[charArray.length * 2 + 1];
        int index = 0;
        for (int i = 0; i < res.length; i++) {
            res[i] = i % 2 == 0 ? '#' : charArray[index++];
        }
        return res;
    }

}

class Manacher {
    
    // 获取最长回文子串的长度
    public int longestPalindromeSubStringLength(String s) {
        if (s == null || s.length() == 0) {
            return 0;
        }
        // 转成manacher字符串数组
        char[] str = getManacherString(s);
        // 回文半径数组
        int[] radius = new int[str.length];
        // 当前最右回文半径的下一个索引
        int right = -1;
        // 当前最右回文半径中心点
        int mid = -1;
        int max = Integer.MIN_VALUE;
        for (int i = 0; i < str.length; i++) {
            // 无需验证区
            // 如果当前计算的节点在最右回文半径内，那么从i向两边扩张，最短的回文半径为Math.min(radius[2 * mid - i], right - i)
            // 如果i关于mid的对称点i'的回文半径被最右回文半径的对称点最左回文半径包裹，那么回文半径一定是i'的回文半径radius[2 * mid - i]
            // 如果i'的回文半径区域压住最左回文半径left或者大于它，那么最少是right - i即left - i'
            // 如果i在最右回文半径的右边，我们尚未统计两侧，其自己构成一个长度为1的回文串
            radius[i] = right > i ? Math.min(radius[2 * mid - i], right - i) : 1;
            // 看看再往两边的字符有没有越界
            // 如果上面的三元命中了如i'区域在最左回文半径内部，下面循环进入一次就直接出来
            while (i + radius[i] < str.length && i - radius[i] >= 0) {
                if (str[i + radius[i]] == str[i - radius[i]]) {
                    // 没越界看看相等否
                    // 相等回文半径+1，不然就结束比对
                    radius[i]++;
                } else {
                    break;
                }
            }
            // 如果最右回文半径变得更右了，更新最右回文半径和其产生时对应的字符
            if (i + radius[i] > right) {
                right = i + radius[i];
                mid = i;
            }
            // 最大回文半径
            max = Math.max(max, radius[i]);
        }
        // manacher串的回文半径可以直接转换成原始字符串中回文串长度
        // "#1#1#" => "11" 3 => 2
        // "#1#2#1#" => "121"  4=> 3
        return max - 1;
    }
    
    // 获取manacher字符数组
    // 121 => #1#2#1#
    private char[] getManacherString(String s) {
        char[] str = s.toCharArray();
        char[] ans = new char[str.length * 2 + 1];
        int index = 0;
        for (int i = 0; i < ans.length; ++i) {
            ans[i] = (i & 1) == 0 ? '#' : str[index++];
        }
        return ans;
    }
    
}
```

---

> ***last change: 2022/11/9***

---
