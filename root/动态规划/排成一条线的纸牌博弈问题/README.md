# 排成一条线的纸牌博弈问题

## 程序员代码面试指南：IT名企算法与数据结构题目最优解（第二版） 第4章 递归和动态规划 排成一条线的纸牌博弈问题

> - ***Question***
>   - 给定一个整型数组 `arr` ，代表数值不同的纸牌排成一条线。
>   - 玩家A和玩家B轮流进行自己的回合，玩家A先手。开始时两个玩家的初始分值都是 `0` 。每一回合，玩家从纸牌数组的任意一端取一个数字（即 `piles[0]` 或 `piles[piles.length - 1]` ），取到的数字将会从数组中移除（数组长度减 `1` ）。玩家选中的数字将会加到他的得分上。当数组中没有剩余数字可取时，游戏结束。
>   - 玩家A和玩家B都绝顶聪明。
>   - 如果玩家A能成为赢家，返回 `true` 。如果两个玩家得分相等，同样认为玩家A是游戏的赢家，也返回 `true` ，否则返回 `false` 。
>   - ***examples:***
>     - 若 `arr = [1, 2, 100, 4]` ：开始时，玩家A只能拿走 `1` 或 `4` 。如果玩家A拿走 `1` ，则排列变为 `[2, 100, 4]` ，接下来玩家B可以拿走 `2` 或 `4` ，然后继续轮到玩家A。如果开始时玩家A拿走 `4` ，则排列变为 `[1, 2, 100]` ，接下来玩家B可以拿走 `1` 或 `100` ，然后继续轮到玩家A。玩家A作为绝顶聪明的人不会先拿 `4` ，因为拿 `4` 之后，玩家B将拿走 `100` 。所以玩家A会先拿 `1` ，让排列变为 `[2, 100, 4]` ，接下来玩家B不管怎么选， `100` 都会被玩家A拿走。玩家A会获胜，分数为 `101` 。所以返回 `101` 。
>     - 若 `arr = [1, 100, 2]` ：开始时，玩家A不管拿 `1` 还是 `2` ，玩家B作为绝顶聪明的人，都会把 `100` 拿走。玩家B会获胜，分数为 `100` 。所以返回 `100` 。
>   - ***tips:***
>     - `1 <= nums.length <= 20`
>     - `0 <= nums[i] <= 10^7`

---

## *Java*

> - ***暴力递归***
>   - ![image](暴力递归.jpg)

```java
class Solution {
    
    public boolean PredictTheWinner(int[] piles) {
        int firstScout = firstHand(piles, 0, piles.length - 1);
        int secondScout = backHand(piles, 0, piles.length - 1);
        return firstScout >= secondScout;
    }
    
    // f(i, j)
    public int firstHand(int[] piles, int left, int right) {
        if (left >= right) {
            return piles[left];
        }
        int getLeft = piles[left] + backHand(piles, left + 1, right);
        int getRight = piles[right] + backHand(piles, left, right - 1);
        return Math.max(getLeft, getRight);
    }
    
    // s(i, j)
    public int backHand(int[] piles, int left, int right) {
        if (left >= right) {
            return 0;
        }
        int getLeft = firstHand(piles, left + 1, right);
        int getRight = firstHand(piles, left, right - 1);
        // 注意本题由于两个玩家都是绝顶聪明的，后手的考虑就会是一种零和博弈，你只能选择先手选择后最差的那一种情况，这是这个题的亮点
        // 因为对手是绝顶聪明的，你作为后手只能吃剩下的
        return Math.min(getLeft, getRight);
    }
    
}
```

> - ***记忆化搜索***

```java
class Solution {
    
    public boolean PredictTheWinner(int[] piles) {
        int len = piles.length;
        int[][] firstHandMap = new int[len][len];
        int[][] backHandMap = new int[len][len];
        for (int i = 0; i < len; ++i) {
            for (int j = 0; j < len; ++j) {
                firstHandMap[i][j] = -1;
                backHandMap[i][j] = -1;
            }
        }
        int firstScout = firstHand(piles, 0, len - 1, firstHandMap, backHandMap);
        int secondScout = backHand(piles, 0, len - 1, firstHandMap, backHandMap);
        return firstScout >= secondScout;
    }
    
    public int firstHand(int[] piles, int left, int right, int[][] firstHandMap, int[][] backHandMap) {
        if (firstHandMap[left][right] != -1) {
            return firstHandMap[left][right];
        }
        if (left >= right) {
            firstHandMap[left][right] = piles[left];
        } else {
            int getLeft = piles[left] + backHand(piles, left + 1, right, firstHandMap, backHandMap);
            int getRight = piles[right] + backHand(piles, left, right - 1, firstHandMap, backHandMap);
            firstHandMap[left][right] = Math.max(getLeft, getRight);
        }
        return firstHandMap[left][right];
    }
    
    public int backHand(int[] piles, int left, int right, int[][] firstHandMap, int[][] backHandMap) {
        if (backHandMap[left][right] != -1) {
            return backHandMap[left][right];
        }
        if (left >= right) {
            backHandMap[left][right] = 0;
        } else {
            int getLeft = firstHand(piles, left + 1, right, firstHandMap, backHandMap);
            int getRight = firstHand(piles, left, right - 1, firstHandMap, backHandMap);
            backHandMap[left][right] = Math.min(getLeft, getRight);
        }
        return backHandMap[left][right];
    }
    
}
```

> - ***动态规划***

```java
class Solution {
    
    public boolean PredictTheWinner(int[] piles) {
        return dp(piles);
    }
    
    public boolean dp(int[] piles) {
        int len = piles.length;
        int[][] firstHandMap = new int[len][len];
        int[][] backHandMap = new int[len][len];
        // 两张表之间是相互依赖的
        // 通过递归函数得知函数变量只有left和right
        // 并且由于这是在一个数组上玩耍，左不会大于右，也就意味着表只有上三角是有用的
        // 先考虑对角线left等于right的时候，也就是递归终止条件
        // 接着通过递归调用得出表每个位置上的依赖关系（本题取决于另外一张表上对称位置的左下两点），那么我们就按照对角线的推移来填表
        for (int i = 0; i < len; i++) {
            firstHandMap[i][i] = piles[i];
            // 数组初始化为0，后手就不需要赋值了
        }
        // 遍历对角线
        for (int j = 1; j < len; j++) {
            // 行
            int left = 0;
            // 列
            int right = j;
            // 因为对角线已经填了，那么斜向从上到下遍历一条对角线，且在矩阵的右上方，越界条件一定是列
            while (right < len) {
                firstHandMap[left][right] = Math.max(piles[left] + backHandMap[left + 1][right], piles[right] + backHandMap[left][right - 1]);
                backHandMap[left][right] = Math.min(firstHandMap[left + 1][right], firstHandMap[left][right - 1]);
                left++;
                right++;
            }
        }
        return firstHandMap[0][len - 1] >= backHandMap[0][len - 1];
    }
    
}
```

---

> ***last change: 2023/1/20***

---
