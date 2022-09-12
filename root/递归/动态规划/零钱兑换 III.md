# [零钱兑换 III]()

### 题目描述
1. arr数组是你的钱包中有的dollar
2. aim是你要凑出来的钱
3. 问有几种凑法
4. 例如：arr={1,2,1,1,2,1,2}，aim=4，有1+1+1+1，1+1+2，2+2，3种凑法

---

### 解题思路
1. 参考[518. 零钱兑换 II](https://leetcode.cn/problems/coin-change-2/)
2. 对于填格子时的优化更多了一步，因为题目限制了钱的张数的缘故，这个空间关系是双向进动的，不像[518. 零钱兑换 II](https://leetcode.cn/problems/coin-change-2/)中每个格子对于下一行的依赖是单向进动的
3. 对于entrySet和Entry要记忆

---

### 代码

```java
// java暴力递归转动态规划
public class Solution {

    //词频统计类
    public static class Info {
        public int[] coins;
        public int[] zhangs;

        public Info(int[] c, int[] z) {
            coins = c;
            zhangs = z;
        }
    }

    public static Info getInfo(int[] arr) {
        HashMap<Integer, Integer> counts = new HashMap<>();
        for (int value : arr) {
            if (!counts.containsKey(value)) {
                counts.put(value, 1);
            } else {
                counts.put(value, counts.get(value) + 1);
            }
        }
        int len = counts.size();
        int[] coins = new int[len];
        int[] zhangs = new int[len];
        int index = 0;
        // 记忆，把键值对转成了视图
        // import java.util.Map.Entry;
        for (Entry<Integer, Integer> entry : counts.entrySet()) {
            coins[index] = entry.getKey();
            zhangs[index] = entry.getValue();
            index++;
        }
        return new Info(coins, zhangs);
    }

    public static int changeByRecursion(int[] arr, int aim) {
        if (arr == null || arr.length == 0 || aim < 0) {
            return 0;
        }
        // 词频统计
        Info info = getInfo(arr);
        return recursion(info.coins, info.zhangs, 0, aim);
    }

    public static int recursion(int[] coins, int[] zhangs, int index, int rest) {
        if (index == coins.length) {
            return rest == 0 ? 1 : 0;
        } else {
            int ways = 0;
            for (int zhang = 0; zhang * coins[index] <= rest && zhang <= zhangs[index]; zhang++) {
                ways += recursion(coins, zhangs, index + 1, rest - (zhang * coins[index]));
            }
            return ways;
        }
    }

    public static int changeByDP1(int[] arr, int aim) {
        if (arr == null || arr.length == 0 || aim < 0) {
            return 0;
        }
        Info info = getInfo(arr);
        int[] coins = info.coins;
        int[] zhangs = info.zhangs;
        int len = coins.length;
        int[][] dp = new int[len + 1][aim + 1];
        dp[len][0] = 1;
        for (int index = len - 1; index >= 0; index--) {
            for (int rest = 0; rest <= aim; rest++) {
                int ways = 0;
                for (int zhang = 0; zhang * coins[index] <= rest && zhang <= zhangs[index]; zhang++) {
                    ways += dp[index + 1][rest - (zhang * coins[index])];
                }
                dp[index][rest] = ways;
            }
        }
        return dp[0][aim];
    }

    public static int changeByDP2(int[] arr, int aim) {
        if (arr == null || arr.length == 0 || aim < 0) {
            return 0;
        }
        Info info = getInfo(arr);
        int[] coins = info.coins;
        int[] zhangs = info.zhangs;
        int len = coins.length;
        int[][] dp = new int[len + 1][aim + 1];
        dp[len][0] = 1;
        // 表的空间结构，其所依赖的下一行会随之移动
        //       -  +
        // -  -+ -+ +
        for (int index = len - 1; index >= 0; index--) {
            for (int rest = 0; rest <= aim; rest++) {
                dp[index][rest] = dp[index + 1][rest];
                if (rest - coins[index] >= 0) {
                    dp[index][rest] += dp[index][rest - coins[index]];
                }
                // 如果我直接加上我左边的，我会多加一个我不需要的，看看有没有再减去
                // zhangs[index] + 1就是下一行依赖的个数，视为间隔会找到多出的那个
                if (rest - coins[index] * (zhangs[index] + 1) >= 0) {
                    dp[index][rest] -= dp[index + 1][rest - coins[index] * (zhangs[index] + 1)];
                }
            }
        }
        return dp[0][aim];
    }

    public static void main(String[] args) {
        int[] arr = new int[]{1, 2, 1, 1, 2, 1, 2};
        System.out.println(changeByRecursion(arr, 4));
        System.out.println(changeByDP1(arr, 4));
        System.out.println(changeByDP2(arr, 4));
    }
}

```
