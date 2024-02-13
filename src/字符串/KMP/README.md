# KMP算法代码模板

## [28. 找出字符串中第一个匹配项的下标](https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string/)

> - ***Question***
>   - KMP算法的用途：给你两个字符串 `haystack` 和 `needle` ，请你在 `haystack` 字符串中找出 `needle` 字符串的第一个匹配项的下标（下标从 `0` 开始）。如果 `needle` 不是 `haystack` 的一部分，则返回 `-1` 。
>   - ***tips:***
>     - `1 <= haystack.length, needle.length <= 10^4`
>     - `haystack` 和 `needle` 仅由小写英文字符组成

---

## *Java*

> - ***KMP大概的原理***
>   - 我们要在 `s1` 中找 `s2` 第一次出现的起始下标：
>     - 将 `s1` 和 `s2` 转成字符数组 `str1` 和 `str2` 。
>     - 准备 `str2` 的 `next` 数组， `next[i]` 记录了 `str2[i]` 之前的字符串中的最长相等前缀与后缀的长度，该前缀与后缀不能是字符串整体：
>       - 假设要求 `str2[i]` 的信息 `next[i]` ：
>         - 看 `str2[i - 1]` 的最长相等前缀的下一个元素等不等于 `str2[i - 1]` ，等于的话 `str2[i] = next[i - 1] + 1`
>         - 否则比对元素变成 `str2[i - 1]` 的最长相等前缀的下一个元素的最长相等前缀的下一个元素
>     - 从 `str1` 的头开始匹配：
>       - 如果两个字符相等，则遍历指针同时后移以比对下一位。
>       - 如果两个字符不等， `str2` 的遍历指针指向当前位置的最长相等前缀的下一个元素，用于表示该元素移动到和当前 `str1` 遍历到的位置对齐。
>       - 如果 `str2` 的头和当前 `str1` 中遍历到的字符不一样，那和暴力方法一样从下一个位置开始对齐。
>   - 非常繁琐，建议看代码理解或者看视频回忆细节。不给出跳过比对区域的证明过程，比较容易想到。
>   - 时间复杂度： `O(n + m)` ，其中 `n` 是字符串 `haystack` 的长度， `m` 是字符串 `needle` 的长度。我们至多需要遍历两字符串一次。
>   - 空间复杂度： `O(m)` ，其中 `m` 是字符串 `needle` 的长度。我们只需要保存字符串 needle 的前缀函数。

```java
class Solution {
    
    public int strStr(String haystack, String needle) {
        // return haystack.indexOf(needle);
        KMP kmp = new KMP();
        return kmp.indexOf(haystack, needle);
    }
    
}

class KMP {
    
    // 在s1中找s2出现的第一个位置，如果没有返回-1
    // 转换为在str1字符数组中找到str2字符数组出现的第一个位置
    public int indexOf(String s1, String s2) {
        // s1比s2短那肯定没有
        // s1和s2为空以及s2长度为0也不用找
        if (s1 == null || s2 == null || s2.length() < 1 || s1.length() < s2.length()) {
            return -1;
        }
        // 转成字符数组
        char[] str1 = s1.toCharArray();
        char[] str2 = s2.toCharArray();
        // 遍历str1的指针
        int p1 = 0;
        // 遍历str2的指针
        int p2 = 0;
        // 获取信息数组
        int[] next = getNextArray(str2);
        while (p1 < str1.length && p2 < str2.length) {
            if (str1[p1] == str2[p2]) {
                // 相等，同时向后遍历
                ++p1;
                ++p2;
            } else if (next[p2] == -1) {
                // 在当前str1段，str2的头已经与str1当前段的尾匹配过了且没有匹配上，str1应该拿下一个段来匹配str2
                // 即s2的开头与s1当前位置p1比就没比对成功（因为if没过），说明s1中的s2一定不以当前p1位置开头，直接从下一个位置开始匹配，和暴力法一样
                ++p1;
            } else {
                // 只要有一个对不上，str2就要右移并将对不上位置的之前的字符串中最长前后缀中前缀的下一个字符对准str1中对不上的那个字符
                // p2指针的回溯等于把str1作为基准向右推移str2直到最长相等前缀与当前p1对齐的过程
                // 我的最长相等前缀的下一个字符和你比
                p2 = next[p2];
            }
        }
        // 如果p2跳出str2长度了，说明比对成功，p1-p2就是第一个匹配开始位置
        // 如果p1越界了，那就是没找到，注意哪怕在后缀中匹配到，p1与p2同时越界，也会判断p2==str2.length
        return p2 == str2.length ? p1 - p2 : -1;
    }
    
    // 获取str2中每一个位置的最长相等前后缀的长度信息数组
    // next[i]表示在str2[i]之前的字符串中，最长的一对相等前缀和后缀的长度（前后缀不能包括前面的字符串整体）
    // 例如"aabaat"，其next[5]为2，前缀aa和后缀aa相等
    private int[] getNextArray(char[] str2) {
        // 把0位置标识为-1代表头，因为0位置前没有前缀
        // 1位置前面的字符串不满足前缀不能覆盖之前的整个字符串的定义，定义为0
        // str2长度只有1或者2的话直接返回
        if (str2.length == 1) {
            return new int[]{-1};
        }
        // 可以省略
        /*if (str2.length == 2) {
            return new int[]{-1, 0};
        }*/
        int[] next = new int[str2.length];
        next[0] = -1;
        next[1] = 0;
        // 遍历str2的指针
        int i = 2;
        // 找到i - 1最长等长前后缀中前缀的下一个字符
        // 如果这个前缀的下一个字符等于str[i - 1]，next[i] = next[i - 1] + 1
        int pointer = 0;
        while (i < next.length) {
            if (str2[i - 1] == str2[pointer]) {
                // 找到了匹配的，某个前缀的下一个字符和str2[i - 1]相等，那么这个前缀的长度+1就是str2[i]的最长相等前后缀长度
                // 先赋值，然后i右移
                // 由于长度等于索引+1，并且pointer右移一位就是其前缀的下一个，可以在计算next[i + 1]时被利用，所以++pointer
                next[i++] = ++pointer;
            } else if (pointer > 0) {
                // 如果没有相等的，pointer就一直回溯，回溯至当前字符的最长相等前后缀中前缀的下一个字符
                pointer = next[pointer];
            } else {
                // 如果指针回溯到前两个字符了，可以判断str[i]前面没有等长的相等前缀和后缀
                next[i++] = 0;
            }
        }
        return next;
    }
    
}
```

---

> ***last change: 2024/2/13***

---
