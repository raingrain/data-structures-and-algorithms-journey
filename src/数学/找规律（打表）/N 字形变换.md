# N 字形变换

## [6. N 字形变换](https://leetcode.cn/problems/zigzag-conversion/)

> - ***Question***
>   - 将一个给定字符串 `s` 根据给定的行数 `numRows` ，以从上往下、从左到右进行 `Z` 字形排列。
>   - 比如输入字符串为 `"PAYPALISHIRING"` 行数为 `3` 时，排列如下：
>     - P   A   H   N
>     - A P L S I I G
>     - Y   I   R
>   - 之后，你的输出需要从左往右逐行读取，产生出一个新的字符串，比如 `"PAHNAPLSIIGYIR"` 。
>   - 请你实现这个将字符串进行指定行数变换的函数：
>   - ***tips:***
>     - `1 <= s.length <= 1000`
>     - `s` 由英文字母（小写和大写）、',' 和 '.' 组成
>     - `1 <= numRows <= 1000`

## Java

> - ***找规律***
>   - 没道理，建议看题解。

```java
class Solution {

    public static String convert(String s, int row) {
        int n = s.length();
        if (row == 1 || row >= n) {
            return s;
        }
        // 变换周期
        int T = 2 * (row - 1);
        char[] ans = new char[n];
        int fill = 0;
        for (int i = 0; i < row; i++) {
            // i : 当前来到第i行
            // j : 一开始的值，就是指 : 当前行应该最先拷贝哪个位置的字符，进入ans！
            for (int j = i, nextColTop = T; j < n; j += T, nextColTop += T) {
                ans[fill++] = s.charAt(j);
                // 如果处在中间行的话！可能会拷贝第二个！
                // 如果处在最初行、最后行的话！不会拷贝第二个！
                if (i >= 1 && i <= row - 2 && nextColTop - i < n) {
                    ans[fill++] = s.charAt(nextColTop - i);
                }
            }
        }
        return String.valueOf(ans);
    }

}
```
