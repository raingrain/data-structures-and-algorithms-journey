# [数组中的较小累加和 II]()

### 题目描述
1. 先参考[数组中的较小累加和 I]()
2. 在此基础上添加累加和数组的限制，如果arr的长度是偶数，你返回那个组的长度应该是一半长，如果arr的长度是奇数，你反回的那个组的长度应该是一半向上和向下取整中累加和小的那一个
3. 可以理解为长度优先

---

### 解题思路
1. 直接参考[数组中的较小累加和 I]()，在其递归函数上添加对次数的限制，也就是累加和数组长度的限制

### 代码

```java
// java暴力递归转动态规划
public class Solution {

    public static int ByRecursion(int[] arr) {
        if (arr == null || arr.length < 2) {
            return 0;
        } else {
            int sum = 0;
            for (int num : arr) {
                sum += num;
            }
            // 由简单的数学逻辑推断，这个累加和一定不会超过sum/2
            // 注意没有负数
            // 奇数情况下我选择的那个组的组数是不确定的，我必须都判断
            return (arr.length & 1) == 0 ? recursion(arr, 0, arr.length / 2, sum / 2) : Math.max(recursion(arr, 0, arr.length / 2, sum / 2), recursion(arr, 0, arr.length / 2 + 1, sum / 2));
        }
    }

    // 在arr[index……]上自由选择，请返回累加和尽量接近rest，但不能超过rest的，最接近rest的
    // 加上picks的限制
    // 从左往右的尝试模型
    public static int recursion(int[] arr, int index, int picks, int rest) {
        if (index == arr.length) {
            // 你都没得选了但还没选完，那肯定无效
            return picks == 0 ? 0 : -1;
        } else {
            int p1 = recursion(arr, index + 1, picks, rest);
            int p2 = -1;
            if (arr[index] <= rest) {
                int next = recursion(arr, index + 1, picks - 1, rest - arr[index]);
                if (next != -1) {
                    p2 = arr[index] + next;
                }
            }
            return Math.max(p1, p2);
        }
    }

    public static int ByDP(int[] arr) {
        if (arr == null || arr.length < 2) {
            return 0;
        } else {
            int sum = 0;
            for (int num : arr) {
                sum += num;
            }
            sum /= 2;
            int x = arr.length;
            // 一个向上取整的技巧，奇数时刻得到大的一半
            int y = (x + 1) / 2;
            int[][][] dp = new int[x + 1][y + 1][sum + 1];
            // 先全部设置成无效
            for (int i = 0; i <= x; i++) {
                for (int j = 0; j <= y; j++) {
                    for (int k = 0; k <= sum; k++) {
                        dp[i][j][k] = -1;
                    }
                }
            }
            // 终止条件
            for (int rest = 0; rest <= sum; rest++) {
                dp[x][0][rest] = 0;
            }
            for (int index = x - 1; index >= 0; index--) {
                for (int picks = 0; picks <= y; picks++) {
                    for (int rest = 0; rest <= sum; rest++) {
                        int p1 = dp[index + 1][picks][rest];
                        int p2 = -1;
                        // picks - 1 >= 0防止越界
                        if (picks - 1 >= 0 && arr[index] <= rest) {
                            int next = dp[index + 1][picks - 1][rest - arr[index]];
                            if (next != -1) {
                                p2 = arr[index] + next;
                            }
                        }
                        dp[index][picks][rest] = Math.max(p1, p2);
                    }
                }
            }
            // sum上面已经处理过了，记得改
            return (arr.length & 1) == 0 ? dp[0][arr.length / 2][sum] : Math.max(dp[0][arr.length / 2][sum], dp[0][arr.length / 2 + 1][sum]);
        }
    }

    public static void main(String[] args) {
        int[] arr = new int[]{33, 24, 35, 30, 27, 26};
        System.out.println(ByRecursion(arr));
        System.out.println(ByDP(arr));
    }
}
```
