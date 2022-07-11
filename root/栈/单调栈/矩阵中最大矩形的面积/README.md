### 题目

- [85. 最大矩形](https://leetcode.cn/problems/maximal-rectangle/)
- [剑指 Offer II 040. 矩阵中最大的矩形](https://leetcode.cn/problems/PLYXKQ/)

---

### 题意

![image](https://user-images.githubusercontent.com/75558694/178192124-040f787f-8b5d-4a1a-9658-aca3af0e42ff.png)

---

### Java实现

#### *思路*

> **- 单调栈法**
>   1. 遍历矩阵中的每一行，把每一行看作一个直方图的x轴，有1的列就是一根柱子，但遇上0时直方图对应列归0，采用数组压缩技巧，每一次遍历基于当前行对数组进行改动；
>   2. 改动后将直方图数组传入[直方图中最大矩形的面积]()的函解题数中，直接获取以当前行作为基底的直方图中矩形面积最大值；
>   3. 不要有遇0时直方图列消失了导致上面矩形被遗忘的疑问，上面矩形的面积在之前的基底中已经被算过了。

#### *代码*

```java
public class Solution {

    // 第85题的输入参数为二维字符数组
    // public int maximalRectangle(char[][] matrix) {
    //     return toHistogramArray(matrix);
    // }

    // 剑指offer的输入参数为一维字符串数组，需要手动转成二维字符数组
    // 并且这题还会有空数组的情况，注意判断
    public int maximalRectangle(String[] matrix) {
        if (matrix == null || matrix.length == 0 || matrix[0].length() == 0) {
            return 0;
        } else {
            // 一维字符串数组转二维字符数组
            char[][] map = new char[matrix.length][matrix[0].length()];
            for (int i = 0; i < matrix.length; i++) {
                map[i] = matrix[i].toCharArray();
            }
            return toHistogramArray(map);
        }
    }

    public int toHistogramArray(char[][] matrix) {
        int[] heights = new int[matrix[0].length];
        int maxArea = 0;
        // 获取每一行的直方图高度数组
        for (int i = 0; i < matrix.length; i++) {
            for (int j = 0; j < matrix[0].length; j++) {
                // 遇上0就删除直方图中的列，遇上1列长度加1
                heights[j] = matrix[i][j] == '0' ? 0 : heights[j] + 1;
            }
            // 某个符合答案的矩形必定以矩阵中的某行作为基底，答案不会遗漏的
            maxArea = Math.max(maxArea, largestRectangleArea(heights));
        }
        return maxArea;
    }

    // 求直方图中矩形面积最大值
    public int largestRectangleArea(int[] heights) {
        int max = 0;
        Stack<Integer> stack = new Stack<>();
        for (int i = 0; i < heights.length; i++) {
            while (!stack.isEmpty() && heights[stack.peek()] >= heights[i]) {
                int min = stack.pop();
                max = Math.max(max, (stack.isEmpty() ? i : (i - stack.peek() - 1)) * heights[min]);
            }
            stack.push(i);
        }
        while (!stack.isEmpty()) {
            int min = stack.pop();
            max = Math.max(max, (stack.isEmpty() ? heights.length : (heights.length - stack.peek() - 1)) * heights[min]);
        }
        return max;
    }
}
```

