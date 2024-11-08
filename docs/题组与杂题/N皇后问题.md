# N皇后问题

## [52. N皇后 II](https://leetcode.cn/problems/n-queens-ii/)

> - **Question 1**
>   - 按照国际象棋的规则，皇后可以攻击与之处在同一行或同一列或同一斜线上的棋子。`n` 皇后问题研究的是如何将 `n` 个皇后放置在 `n × n` 的棋盘上，并且使皇后彼此之间不能相互攻击。
>   - 给你一个整数 `n` ，返回 `n` 皇后问题不同的解决方案的数量。
>   - **Tips**
>     - `1 <= n <= 9`

## [51. N 皇后](https://leetcode.cn/problems/n-queens/)

## [面试题 08.12. 八皇后](https://leetcode.cn/problems/n-queens-ii/)

> - **Question 2**
>   - 给你一个整数 `n` ，返回所有不同的 `n` 皇后问题的解决方案。每一种解法包含一个不同的 `n` 皇后问题的棋子放置方案，该方案中 'Q' 和 '.' 分别代表了皇后和空位。
>   - 如 `n = 4` 输出 `[[".Q..", "...Q", "Q...", "..Q."], ["..Q.", "Q...", "...Q", ".Q.."]]` 。
>   - **Tips**
>     - `1 <= n <= 9`

## Java

> - **暴力递归**

```java
class Solution {
    
    // 只求数量
    public int totalNQueens(int n) {
        // record[i] = j 指i行的皇后放在j列上
        int[] record = new int[n];
        List<List<String>> ans = new ArrayList<>();
        return recursion(n, record, 0, ans);
    }
    
    // 求所有解法
    public List<List<String>> solveNQueens(int n) {
        int[] record = new int[n];
        List<List<String>> ans = new ArrayList<>();
        recursion(n, record, 0, ans);
        return ans;
    }
    
    // 我们一行返回一个皇后，就解决了皇后不在同一行上的限制
    // 当前来到第row行，在所有列上都尝试放皇后，求row行以后合法的放法
    public int recursion(int n, int[] record, int row, List<List<String>> ans) {
        if (row == n) {
            ans.add(addAns(record, n));
            return 1;
        }
        int res = 0;
        for (int col = 0; col < n; ++col) {
            // 合法才放皇后，并继续放皇后
            if (isVaild(record, row, col)) {
                record[row] = col;
                res += recursion(n, record, row + 1, ans);
            }
        }
        return res;
    }
    
    public boolean isVaild(int[] record, int row, int col) {
        for (int i = 0; i < row; ++i) {
            // 如果当前选择和之前的选择之间，违背了不在同一列且不在同一斜线上的规则，返回false
            if (col == record[i] || Math.abs(row - i) == Math.abs(col - record[i])) {
                return false;
            }
        }
        // 否则就是合理的选择
        return true;
    }
    
    public List<String> addAns(int[] record, int n) {
        List<String> list = new ArrayList<>();
        for (int i = 0; i < n; ++i) {
            // 先全部填成点
            char[] str = new char[n];
            Arrays.fill(str, '.');
            // 在把对应列变成Q
            str[record[i]] = 'Q';
            list.add(new String(str));
        }
        return list;
    }
    
}
```

> - **暴力递归 + 位运算优化**

```java
class Solution {
    
    // 只求数量
    public int totalNQueens(int n) {
        // 假定是一个8皇后问题。
        // limit为……0011111111
        // 1的含义代表棋盘上的所有列都被填满，用来终止递归
        int limit = (1 << n) - 1;
        int[] record = new int[n];
        List<List<String>> ans = new ArrayList<>();
        return recursion(n, limit, 0, 0, 0, 0, record, ans);
    }
    
    // 求所有解法
    public List<List<String>> solveNQueens(int n) {
        int limit = (1 << n) - 1;
        int[] record = new int[n];
        // 最后返回结果数组
        List<List<String>> ans = new ArrayList<>();
        recursion(n, limit, 0, 0, 0, 0, record, ans);
        return ans;
    }
    
    // 递归含义：现在来到第row行，前面的每一行都只有一个皇后，之前的选择都满足不同行不同列不同斜线，请你求出从当前行开始到最后一行的所有填法中满足条件的个数。
    // 参数的含义分别为：棋盘大小n，二进制为n个1的limit，接下来的三个参数的二进制分别代表哪些列、哪些主对角线和副对角线的位置上不能填皇后（二进制位1，能填的位置为0），填法记录数组，求所有解法时要返回的数组。
    public int recursion(int n, int limit, int row, int colLimit, int leftDownLimit, int rightDownLimit, int[] record, List<List<String>> ans) {
        if (limit == colLimit) {
            // 你居然能让colLimit的二进制的后n位全是1，说明你成功了一种方法
            // 在终止条件时将路径加入结果数组
            ans.add(addAns(record, n));
            return 1;
        }
        // 这个变量表示你当前有哪些列能填
        // 如果是8皇后问题
        // 三个限制条件一或，得到结果为……00011100
        // 取反有……11100011
        // 再与一下11111111
        // 有11100011，即available二进制位上为1的位置就是能填的
        int available = limit & (~(colLimit | leftDownLimit | rightDownLimit));
        int mostRightOne = 0;
        int res = 0;
        while (available != 0) {
            // 与上相反数得到最右侧的1
            mostRightOne = available & (~available + 1);
            // 使用过后要减掉
            available -= mostRightOne;
            // 对于8 ……00001000 来说，Integer.bitCount(8)返回1
            // 对于7 ……00000111 来说，Integer.bitCount(7)返回3
            // 让二进制位中的从右往左数第8位作为棋盘的第一列（0开始），使用如下公式可以算出最右侧的1距离左侧的边界，然后记录在路径中
            record[row] = n - Integer.bitCount(mostRightOne - 1) - 1;
            // 每个最右侧的1都算一遍然后相加，行+1，列加上最右侧的1，斜线方向加上后由于要去下一行，还要在左移右移一下
            res += recursion(n, limit, row + 1, colLimit | mostRightOne, (leftDownLimit | mostRightOne) << 1, (rightDownLimit | mostRightOne) >>> 1, record, ans);
        }
        return res;
    }
    
    public List<String> addAns(int[] record, int n) {
        List<String> list = new ArrayList<>();
        for (int i = 0; i < n; ++i) {
            char[] str = new char[n];
            Arrays.fill(str, '.');
            str[record[i]] = 'Q';
            list.add(new String(str));
        }
        return list;
    }
    
}
```
