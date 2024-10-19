# Excel表列名称和序号之间的转换

## [168. Excel表列名称](https://leetcode.cn/problems/excel-sheet-column-title/)

> - ***Question 1***
>   - 给你一个整数 `columnNumber` ，返回它在 `Excel` 表中相对应的列名称。
>   - `A-Z -> 1-26`
>   - `AA -> 27, AB -> 28, ...`
>   - ***tips:***
>     - `1 <= columnNumber <= 2^31 - 1`

## [171. Excel 表列序号](https://leetcode.cn/problems/excel-sheet-column-number/)

> - ***Question 2***
>   - 给你一个字符串 `columnTitle` ，表示 `Excel` 表格中的列名称。返回该列名称对应的列序号。
>   - ***tips:***
>     - `1 <= columnTitle.length <= 7`
>     - `columnTitle` 仅由大写英文组成
>     - `columnTitle` 在范围 `["A", "FXSHRXW"]` 内

## Java

> - ***Question 1: 整数转伪26进制***
>   - 这是一道从 `1` 开始的的 `26` 进制转换题。
>   - 对于一般性的进制转换题目，只需要不断地对 `columnNumber` 进行 `%` 运算取得最后一位，然后对 `columnNumber` 进行 `/` 运算，将已经取得的位数去掉，直到 `columnNumber` 为 `0` 可。
>   - 一般性的进制转换题目无须进行额外操作，是因为我们是在每一位数值范围在 `[0, x)` 的前提下进行逢 `x` 进一。
>   - 但本题需要我们将从 `1` 开始，因此在执行进制转换操作前，我们需要先对 `columnNumber` 执行减一操作，从而实现整体偏移。

```java
class Solution {
    
    public String convertToTitle(int columnNumber) {
        StringBuilder builder = new StringBuilder();
        while (columnNumber > 0) {
            // 减1是因为从1开始
            columnNumber--;
            builder.append((char) (columnNumber % 26 + 'A'));
            columnNumber /= 26;
        }
        builder.reverse();
        return builder.toString();
    }
    
}
```

> - ***Question 2: 伪26进制转整数***

```java
class Solution {
    
    public int titleToNumber(String columnTitle) {
        char[] str = columnTitle.toCharArray();
        int ans = 0;
        for (char c : str) {
            // 加1是因为从1开始
            ans = ans * 26 + (c - 'A') + 1;
        }
        return ans;
    }
    
}
```
