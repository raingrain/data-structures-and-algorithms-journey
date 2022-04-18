### 解题思路
1. 创建一个长度等于amount+1的数组，表示金额为从0到amount的每一种金额所能找到的凑成总金额所需的最少的硬币个数，全部初始化为-1，表示没有一种组合凑得齐，dp[0]初始化为0，需要0个硬币；
2. 两个循环，一个遍历dp来找到每一种金额的最优解，内循环遍历面额数组，当面额小于总金额且dp[当前金额-面额]不等于-1时，可以参与计算，当dp[当前金额] == -1（为初始状态）或者之前的最优解比现在的大时，对最优解进行一个替换的操作，最后返回dp[-1]；
3. (dp[i] = -1 if dp[i - coin] == -1) or (dp[i] = 1 + min(dp[i - coin]) if dp[i - coin] != -1) if {coin <= amount}

### 代码

```python3 []
# python动态规划
class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        dp = [-1] * (amount + 1)
        dp[0] = 0
        for i in range(1, amount + 1):
            for coin in coins:
                if coin <= amount and dp[i - coin] != -1:
                    if dp[i] == -1 or dp[i] > (dp[i - coin] + 1):
                        dp[i] = dp[i - coin] + 1 
        return dp[-1]
```
