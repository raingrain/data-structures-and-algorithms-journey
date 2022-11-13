# 前缀和代码模板

## [303. 区域和检索 - 数组不可变](https://leetcode.cn/problems/range-sum-query-immutable/)

> - ***Question 1***
>   - 给你一个数组 `nums` ，实现 `NumArray` 类：
>     - `NumArray(int[] nums)` 用整数数组 `nums` 初始化对象。
>     - `int sumRange(int left, int right)` 返回数组 `nums` 中索引 `left` 和索引 `right` 之间的 `nums` 元素的和。
>   - ***tips:***
>     - `1 <= nums.length <= 3 * 10^4`
>     - `-10^5 <= nums[i] <= 10^5`
>     - `0 <= index < nums.length`
>     - `-100 <= val <= 100`
>     - `0 <= left <= right < nums.length`
>     - 调用 `sumRange` 方法次数不大于 `3 * 10^4`

## [304. 二维区域和检索 - 矩阵不可变](https://leetcode.cn/problems/range-sum-query-2d-immutable/)

## [剑指 Offer II 013. 二维子矩阵的和](https://leetcode.cn/problems/O4NDxx/)

> - ***Question 2***
>   - 给定一个二维矩阵 `matrix` ，实现 `NumMatrix` 类：
>     - `NumMatrix(int[][] matrix)` 给定整数矩阵 `matrix` 进行初始化。
>     - `int sumRegion(int row1, int col1, int row2, int col2)` 返回左上角 `(row1, col1)` 、右下角 `(row2, col2)` 所描述的子矩阵的元素总和。
>   - ***tips:***
>     - `m == matrix.length`
>     - `n == matrix[i].length`
>     - `1 <= m, n <= 200`
>     - `-10^5 <= matrix[i][j] <= 10^5`
>     - `0 <= row1 <= row2 < m`
>     - `0 <= col1 <= col2 < n`
>     - 最多调用 `10^4` 次 `sumRegion` 方法

---

## *Java*

> - ***一维前缀和及区间和数组递推公式***
>   - `preSum[0] = arr[0]`
>   - `preSum[i] = preSum[i - 1] + arr[i]`
>   - `sum[i ~ j] = preSum[j] - preSum[i - 1] (i > 0)`
>   - `sum[i ~ j] = preSum[j] (i = 0)`

```java
class NumArray {
    
    private final int[] preSum;
    
    public NumArray(int[] nums) {
        this.preSum = new int[nums.length];
        this.preSum[0] = nums[0];
        for (int i = 1; i < nums.length; ++i) {
            preSum[i] = preSum[i - 1] + nums[i];
        }
    }
    
    public int sumRange(int left, int right) {
        return left == 0 ? preSum[right] : preSum[right] - preSum[left - 1];
    }
    
}
```

> - ***二维前缀和及区间和数组递推公式***
>   - `sum[(0, 0) ~ (i, j)] = preSum[i][j] = preSum[i − 1][j]+preSum[i][j−1] − preSum[i − 1][j − 1] + matrix[i][j]`
>   - `sum[(row1, col1) ~ (row2, col2)] = preSum[row2][col2] − preSum[row2][col1 − 1] − preSum[row1 − 1][col2] + preSum[row1 − 1][col1 − 1]`
>   - 为避免 `0` 时的讨论，请将二维数组的 `preSum` 数组行列增加 `1` 且从 `(1, 1)` 开始

```java
class NumMatrix {
    
    private int[][] preSum;
    
    public NumMatrix(int[][] matrix) {
        if (matrix.length > 0) {
            preSum = new int[matrix.length + 1][matrix[0].length + 1];
            for (int i = 0; i < matrix.length; ++i) {
                for (int j = 0; j < matrix[0].length; ++j) {
                    preSum[i + 1][j + 1] = preSum[i][j + 1] + preSum[i + 1][j] - preSum[i][j] + matrix[i][j];
                }
            }
        }
    }
    
    public int sumRegion(int row1, int col1, int row2, int col2) {
        return preSum[row2 + 1][col2 + 1] - preSum[row1][col2 + 1] - preSum[row2 + 1][col1] + preSum[row1][col1];
    }
    
}
```

---

> ***last change: 2022/11/13***

---
