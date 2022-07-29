# [303. 区域和检索 - 数组不可变](https://leetcode.cn/problems/range-sum-query-immutable/)

![image](https://user-images.githubusercontent.com/75558694/180650090-b4271327-6de4-4e4f-9e71-ef5ab8c2426c.png)

# [304. 二维区域和检索 - 矩阵不可变](https://leetcode.cn/problems/range-sum-query-2d-immutable/)
# [剑指 Offer II 013. 二维子矩阵的和](https://leetcode.cn/problems/O4NDxx/)

![image](https://user-images.githubusercontent.com/75558694/180642793-ac58ea12-a0d8-4e6b-88f8-5ee57fce82ef.png)

---

### *Java*

> - ***一维前缀和及区间和数组递推公式***
>   - `preSum[0] = arr[0]`
>   - `preSum[i] = preSum[i - 1] + arr[i]`
>   - `Sum[i ~ j] = preSum[j] - preSum[i - 1]` (i > 0)
>   
> - ***二维前缀和及区间和数组递推公式***
>   - sum[(0, 0) ~ (i, j)] = `preSum[i][j] = preSum[i − 1][j]+preSum[i][j−1] − preSum[i − 1][j − 1] + matrix[i][j]`
>   - sum[(row1, col1) ~ (row2, col2)] = `preSum[row2][col2] − preSum[row2][col1 − 1] − preSum[row1 − 1][col2] + preSum[row1 − 1][col1 − 1]`
>   - 为避免0时的讨论，请将二维数组的preSum数组行列增加1且从(1, 1)开始

```java
// 一维
class NumArray {

    private int[] prefix;

    public NumArray(int[] nums) {
        prefix = new int[nums.length];
        prefix[0] = nums[0];
        for (int i = 1; i < nums.length; i++) {
            prefix[i] = prefix[i - 1] + nums[i];
        }
    }
    
    public int sumRange(int left, int right) {
        return left == 0 ? prefix[right] : prefix[right] - prefix[left - 1];
    }
}

/**
 * Your NumArray object will be instantiated and called as such:
 * NumArray obj = new NumArray(nums);
 * int param_1 = obj.sumRange(left,right);
 */
 
 
// 二维
class NumMatrix {

    private int[][] preSum;

    public NumMatrix(int[][] matrix) {
        if (matrix.length > 0) {
            preSum = new int[matrix.length + 1][matrix[0].length + 1];
            for (int i = 0; i < matrix.length; i++) {
                for (int j = 0; j < matrix[0].length; j++) {
                    preSum[i + 1][j + 1] = preSum[i][j + 1] + preSum[i + 1][j] - preSum[i][j] + matrix[i][j];
                }
            }
        }
    } 
    
    public int sumRegion(int row1, int col1, int row2, int col2) {
        return preSum[row2 + 1][col2 + 1] - preSum[row1][col2 + 1] - preSum[row2 + 1][col1] + preSum[row1][col1];
    }
}

/**
 * Your NumMatrix object will be instantiated and called as such:
 * NumMatrix obj = new NumMatrix(matrix);
 * int param_1 = obj.sumRegion(row1,col1,row2,col2);
 */
```
