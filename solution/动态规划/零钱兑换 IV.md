# [零钱兑换 IV]()

### 题目描述
1. 你的钱包arr里面有一些钱，每一张都是不同国家的，请你凑出指定面值aim，返回方法数
2. 例如，arr={1,1,1}，你的钱包里面有一张美元一张欧元一张日元，请你凑出面值为2，很显然答案为3

---

### 解题思路
1. 从左往右的尝试模型

---

### 代码

```java
// java暴力递归转动态规划
public class Solution {

    public static int changeByRecursion(int[] arr, int aim) {
        return recursion(arr, 0, aim);
    }

    // 返回arr[index……]上，拿rest面额所需的方法数
    public static int recursion(int[] arr, int index, int rest) {
        if (rest < 0) {
            return 0;
        } else if (index == arr.length) {
            return rest == 0 ? 1 : 0;
        } else {
            // 对于每一个index来说只有拿或者不拿
            return recursion(arr, index + 1, rest) + recursion(arr, index + 1, rest - arr[index]);
        }
    }

    public static int changeByDP(int[] arr, int aim) {
        if (aim == 0) {
            return 0;
        } else {
            int len = arr.length;
            int[][] dp = new int[len + 1][aim + 1];
            dp[len][0] = 1;
            for (int index = len - 1; index >= 0; index--) {
                for (int rest = 0; rest <= aim; rest++) {
                    // 记得对下标越界做一个判断
                    dp[index][rest] = dp[index + 1][rest] + (rest - arr[index] >= 0 ? dp[index + 1][rest - arr[index]] : 0);
                }
            }
            return dp[0][aim];
        }
    }

    public static void main(String[] args) {
        int[] arr = new int[]{1, 1, 1};
        System.out.println(changeByRecursion(arr, 2));
        System.out.println(changeByDP(arr, 2));
    }
}
```
