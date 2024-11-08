# 在LR字符串中交换相邻字符

## [777. 在LR字符串中交换相邻字符](https://leetcode.cn/problems/swap-adjacent-in-lr-string/)

> - **Question**
>   - 在一个由 `'L' , 'R', 'X'` 三个字符组成的字符串（例如 `"RXXLRXRXL"` ）中进行移动操作。一次移动操作指用一个 `"LX"` 替换一个 `"XL"` ，或者用一个 `"XR"` 替换一个 `"RX"` 。现给定起始字符串 `start` 和结束字符串 `end` ，请编写代码，当且仅当存在一系列移动操作使得 `start` 可以转换成 `end` 时，返回 `True` 。
>   - **Tips**
>     - `1 <= len(start) = len(end) <= 10000`
>     - `start` 和 `end` 中的字符串仅限于 `'L', 'R', 'X'`

## Java

> - **双指针**
>   - ![image](./images/在LR字符串中交换相邻字符.png)

```java
class Solution {

    public boolean canTransform(String start, String end) {
        int n = start.length();
        int i = 0, j = 0;
        while (i < n && j < n) {
            while (i < n && start.charAt(i) == 'X') {
                i++;
            }
            while (j < n && end.charAt(j) == 'X') {
                j++;
            }
            if (i < n && j < n) {
                if (start.charAt(i) != end.charAt(j)) {
                    return false;
                }
                char c = start.charAt(i);
                if ((c == 'L' && i < j) || (c == 'R' && i > j)) {
                    return false;
                }
                i++;
                j++;
            }
        }
        while (i < n) {
            if (start.charAt(i) != 'X') {
                return false;
            }
            i++;
        }
        while (j < n) {
            if (end.charAt(j) != 'X') {
                return false;
            }
            j++;
        }
        return true;
    }

}
```
