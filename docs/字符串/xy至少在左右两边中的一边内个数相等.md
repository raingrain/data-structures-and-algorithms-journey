# xy至少在左右两边中的一边内个数相等

## 微软

> - **Question**
>   - 比如 `str = "ayxbx"` 有以下 `4` 种切法 `a | yxbx, ay | xbx, ayx | bx, ayxb | x` ，其中第 `1, 3, 4` 种切法符合 `x` 和 `y` 的个数，至少在左右两块中的一块里有相同的数量，所以返回 `3` 。
>   - 给定一个字符串 `str` ，长度为 `N` ，你有 `N - 1` 种划分方法，把 `str` 切成左右两半，返回有几种切法满足 `x` 和 `y` 的个数至少在左右两块中的一块里有相同的数量。

## Java

> - **词频统计 + 一次扫描**

```java
class Solution {

    public int splitSameNumberWays(char[] str) {
        if (str == null || str.length == 0) {
            return 0;
        }
        // 一共有几个x和几个y
        int xAll = 0;
        int yAll = 0;
        for (char c : str) {
            xAll += c == 'x' ? 1 : 0;
            yAll += c == 'y' ? 1 : 0;
        }
        // 切割线左部分几个x和y
        int leftX = str[0] == 'x' ? 1 : 0;
        int leftY = str[0] == 'y' ? 1 : 0;
        int ans = 0;
        // 枚举所有切割线
        for (int i = 1; i < str.length; i++) {
            if (leftX == leftY || (xAll - leftX) == (yAll - leftY)) {
                // 左右两边有一边xy相等答案+1
                ans++;
            }
            leftX += str[i] == 'x' ? 1 : 0;
            leftY += str[i] == 'y' ? 1 : 0;
        }
        return ans;
    }

}
```
