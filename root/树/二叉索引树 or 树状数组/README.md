# [307. 区域和检索 - 数组可修改](https://leetcode.cn/problems/range-sum-query-mutable/)

![image](https://user-images.githubusercontent.com/75558694/180632845-b5efd08b-dbc8-4307-8b9a-3d2d2405564f.png)

# [304. 二维区域和检索 - 矩阵不可变](https://leetcode.cn/problems/range-sum-query-2d-immutable/)
# [剑指 Offer II 013. 二维子矩阵的和](https://leetcode.cn/problems/O4NDxx/)

![image](https://user-images.githubusercontent.com/75558694/180642793-ac58ea12-a0d8-4e6b-88f8-5ee57fce82ef.png)

# [308. 二维区域和检索 - 可变](https://leetcode.cn/problems/range-sum-query-2d-mutable/)

---

### *Java*

> - ***一维数组的树状数组***
>   - 树状数组的形状可以直接参考百度上的；
>   - 树状数组选择从1开始是为了与改变某个位置的值时能够迅速找到受影响的位置二进制规律相结合的；
>   - 对于通过对和与自己的相反数得到的数进行相加减得到的二进制规律不要深究；
>   - 保存一份原始数组，并在update和add时同时保持更新，树状数组的修改在addInIndexTree()上。 
>    
> - ***二维数组的树状数组***
>   - 直接将一维数组中的二进制规律推广到二维矩阵中的行和列上；
>   - 算一个区域的和需要用到一些几何方面的知识，即大减小再把多减的一份加回来；
>   - 记得初次更新要在addInIndexTree中更新，在update中更新会有全0的错误；
>   - 其他步骤和一维数组一致。

```java
// 以下为一维数组的实现
class NumArray {

    // 用来保存数组中发生的变动以做到更新
    public int[] nums;
    private int[] tree;

    public NumArray(int[] nums) {
        this.nums = nums;
        // 树状数组向后偏移一位
        tree = new int[nums.length + 1];
        // 初始化树状数组
        for (int i = 0; i < nums.length; i++) {
            addInIndexTree(i + 1, nums[i]);
        }
    }

    // 单点更新
    public void update(int index, int val) {
        addInIndexTree(index + 1, val - nums[index]);
        nums[index] = val;
    }

    // 这种规律不用记住
    // 获取由index所对应的二进制数的最后一个1及其右边所有0组成的数
    private int getRightMostOne(int index) {
        // 一个数与上自己的相反数（取反 + 1）
        return index & -index;
    }

    public void add(int index, int val) {
        addInIndexTree(index + 1, val);
        nums[index] += val;
    }

    // 单点增加或减少
    private void addInIndexTree(int index, int val) {
        // 更改index位置变动后树状数组中受影响的位置
        while (index < tree.length) {
            tree[index] += val;
            // 和自己最后一个1相加，改变后再相加，加到越界为止
            // 其中所得到的位置就是包括了index位置的累加和
            index += getRightMostOne(index);
        }
    }

    // 求树状数组中1-index的前缀和
    private int preFixSum(int index) {
        int sum = 0;
        while (index > 0) {
            sum += tree[index];
            // 和add同理，只不过换成减法，找到我前面的所有分前缀和
            index -= getRightMostOne(index);
        }
        return sum;
    }

    // 求原数组中left-right的前缀和
    public int sumRange(int left, int right) {
        // 由于树状数组往后推移1位的特性，不需要考虑left等于0时的越界问题
        return preFixSum(right + 1) - preFixSum(left);
    }
}

/**
 * Your NumArray object will be instantiated and called as such:
 * NumArray obj = new NumArray(nums);
 * obj.update(index,val);
 * int param_2 = obj.sumRange(left,right);
 */

 
// 以下为二维数组的实现
class NumMatrix {

    public int[][] matrix;
    public int[][] tree;
    private final int hang;
    private final int lie;

    public NumMatrix(int[][] matrix) {
        this.matrix = matrix;
        hang = matrix.length;
        lie = matrix[0].length;
        tree = new int[hang + 1][lie + 1];
        for (int i = 0; i < hang; i++) {
            for (int j = 0; j < lie; j++) {
                // 不能调用update，否则由于this.matrix已经赋值导致val - matrix[row][col]等于0进而使结果全为0
                addInInterVal(i + 1, j + 1, matrix[i][j]);
            }
        }
    }

    public void update(int row, int col, int val) {
        addInInterVal(row + 1, col + 1, val - matrix[row][col]);
        matrix[row][col] = val;
    }

    // 获取由index所对应的二进制数的最后一个1及其右边所有0组成的数
    private int getRightMostOne(int index) {
        // 一个数与上自己的相反数（取反 + 1）
        return index & -index;
    }

    public void add(int row, int col, int val) {
        addInInterVal(row + 1, col + 1, val);
        matrix[row][col] += val;
    }

    // 下面两个传的都是树中的位置
    private void addInInterVal(int row, int col, int val) {
        for (int i = row; i <= hang; i += getRightMostOne(i)) {
            for (int j = col; j <= lie; j += getRightMostOne(j)) {
                tree[i][j] += val;
            }
        }
    }

    private int preFixSum(int row, int col) {
        int ans = 0;
        for (int i = row; i > 0; i -= getRightMostOne(i)) {
            for (int j = col; j > 0; j -= getRightMostOne(j)) {
                ans += tree[i][j];
            }
        }
        return ans;
    }

    public int sumRegion(int row1, int col1, int row2, int col2) {
        // 避免因为左上角的越界而判空
        return preFixSum(row2 + 1, col2 + 1) - preFixSum(row1, col2 + 1) - preFixSum(row2 + 1, col1) + preFixSum(row1, col1);
    }
}

/**
 * Your NumArray object will be instantiated and called as such:
 * NumArray obj = new NumArray(nums);
 * obj.update(index,val);
 * int param_2 = obj.sumRange(left,right);
 */
```
