### 题目

- [84. 柱状图中最大的矩形](https://leetcode.cn/problems/largest-rectangle-in-histogram/)
- [剑指 Offer II 039. 直方图最大矩形面积](https://leetcode.cn/problems/0ynMMM/)

---

### 题意

![image](https://user-images.githubusercontent.com/75558694/178145320-4983e926-6e4f-4425-b226-399bcd08c4e7.png)

---

### Java实现

#### *思路*

> **- 单调栈法**
>   - 参考[1856. 子数组最小乘积的最大值](https://leetcode.cn/problems/maximum-subarray-min-product/)的思路，在计算结果时变成乘以区间长度即可

#### *代码*

```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        // 面积不可能为负数
        int max = -1;
        // 单调栈
        Stack<Integer> stack = new Stack<>();
        for (int i = 0; i < heights.length; i++) {
            while (!stack.isEmpty() && heights[stack.peek()] >= heights[i]) {
                int min = stack.pop();
                // 区间长度乘以高度
                max = Math.max(max, (stack.isEmpty() ? i : (i - stack.peek() - 1)) * heights[min]);
            }
            stack.push(i);
        }
        // 结算单调栈中元素
        while (!stack.isEmpty()) {
            int min = stack.pop();
            max = Math.max(max, (stack.isEmpty() ? heights.length : (heights.length - stack.peek() - 1)) * heights[min]);
        }
        return max;
    }
}
```
