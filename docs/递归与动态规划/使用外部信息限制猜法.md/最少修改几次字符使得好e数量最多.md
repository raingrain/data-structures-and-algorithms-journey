# 最少修改几次字符使得好e数量最多

## 网易

> - **Question**
>   - 小红拿到了一个仅由 `r, e, d` 组成的字符串。
>   - 她定义一个字符 `e` 为“好e”：当且仅当这个 `e` 字符和 `r, d` 相邻。例如 `reeder` 只有一个“好e”，前两个 `e` 都不是“好e”，只有第三个 `e` 是“好e”。
>   - 小红每次可以将任意字符修改为任意字符，即三种字符可以相互修改。
>   - 她希望“好e”的数量尽可能多，小红想知道，自己最少要修改多少次。

## Java

> - **动态规划**

```java
class Solution {

    // // int[] arr { d 0  e 1  r 2  }
// // 好e -> 好1
// // arr[i....] 请整出最多的好1来！
// // 返回1：整出多少个好1
// // 返回2: 低价是多少？
// // 返回最小代价
// //  prepre    pre
// // i-2       i-1
//
//
//
// // arr[0 0 0] 好1 左右0、2双全
// //     0 1 2
// //
// // 1)  0 0 i  mostEminCost(arr, 2, 0 , 0)
// // 2)  0 1 i  mostEminCost(arr, 2, 0 , 1)
// // 3)  0 2 i
// // 4)  1 0 i
// // 5)  1 1 i
// //
// // i 2 * 10^5
// // prepre 0 1 2
// // pre    0 1 2
// // 9 * 2 * 10^5
// public static Info mostEminCost(int[] arr, int i, int prepre, int pre) {
//  if(i == arr.length) {
//   return new Info(0,0);
//  }
//  // i位置不终止
//  // 可能性1 ： [i] -> 0
//  int curCost1 = arr[i] == 0 ? 0 :1;
//  int curValue1 = prepre == 2 && pre == 1 ? 1 : 0;
//  Info info1 = mostEminCost(arr, i + 1, pre, 0);
//
//
//  // 可能性2 ： [i] -> 1
//  int curCost2 = arr[i] == 1 ? 0 : 1;
//  int curValue2 = 0;
//  Info info2 = mostEminCost(arr, i + 1, pre, 1);
//
//
//  // 可能性3 ： [i] -> 2
//  int curCost3 = arr[i] == 2 ? 0 : 1;
//  int curValue3 = prepre == 0 && pre == 1 ? 1 : 0;
//  Info info3 = mostEminCost(arr, i + 1, pre, 2);
//
//  int p1Value = curValue1 + info1.value;
//  int p1Cost = curCost1 + info1.cost;
//  int p2Value = curValue2 + info2.value;
//  int p2Cost = curCost2 + info2.cost;
//  int p3Value = curValue3 + info3.value;
//  int p3Cost = curCost3 + info3.cost;
//  int bestValue = 0;
//  int minCost = Integer.MAX_VALUE;
//
//  if(bestValue < p1Value) {
//   bestValue = p1Value;
//   minCost = p1Cost;
//  } else if(bestValue == p1Value) {
//   minCost = Math.min(minCost, p1Cost);
//  }
//  if(bestValue < p2Value) {
//   bestValue = p2Value;
//   minCost = p2Cost;
//  } else if(bestValue == p2Value) {
//   minCost = Math.min(minCost, p2Cost);
//  }
//  if(bestValue < p3Value) {
//   bestValue = p3Value;
//   minCost = p3Cost;
//  } else if(bestValue == p3Value) {
//   minCost = Math.min(minCost, p3Cost);
//  }
//  return new Info(bestValue, minCost);
// }
//
// public static class Info{
//  public int value;
//  public int cost;
//
//  public Info(int v, int c) {
//   value = v;
//   cost = c;
//  }
// }

    public static int minCost(String str) {
        int n = str.length();
        if (n < 3) {
            return -1;
        }
        int[] arr = new int[n];
        // d认为是0，e认为是1，r认为是2
        for (int i = 0; i < n; i++) {
            char cur = str.charAt(i);
            if (cur == 'd') {
                arr[i] = 0;
            } else if (cur == 'e') {
                arr[i] = 1;
            } else {
                arr[i] = 2;
            }
        }
        // 通过上面的转化，问题变成了：
        // 1的左右，一定要被0和2包围，这个1才是"好1"
        // 请让"好1"的尽量多，返回最少的修改代价
        int maxGood = 0;
        int minCost = Integer.MAX_VALUE;
        for (int prepre = 0; prepre < 3; prepre++) {
            for (int pre = 0; pre < 3; pre++) {
                int cost = arr[0] == prepre ? 0 : 1;
                cost += arr[1] == pre ? 0 : 1;
                Info cur = process(arr, 2, prepre, pre);
                if (cur.maxGood > maxGood) {
                    maxGood = cur.maxGood;
                    minCost = cur.minCost + cost;
                } else if (cur.maxGood == maxGood) {
                    minCost = Math.min(minCost, cur.minCost + cost);
                }
            }
        }
        return minCost;
    }

    public static class Info {

        public int maxGood;
        public int minCost;

        public Info(int a, int b) {
            maxGood = a;
            minCost = b;
        }

    }

    // 暴力递归
    // 可以自己改成动态规划
    // arr[index-2]位置的数值是prepre
    // arr[index-1]位置的数值是pre
    // 在这种情况下，请让arr[index...]上的好1尽量多
    // 返回:
    // 尽量多的"好1"，是多少？
    // 得到尽量多的"好1"，最小代价是多少?
    public static Info process(int[] arr, int index, int prepre, int pre) {
        if (index == arr.length) {
            return new Info(0, 0);
        }
        // 可能性1，arr[index]，变成0，看看能不能让pre变成好e
        int p1Value = prepre == 2 && pre == 1 ? 1 : 0;
        // 看看自己是不是要修改的字符
        int p1Cost = arr[index] == 0 ? 0 : 1;
        Info info = process(arr, index + 1, pre, 0);
        p1Value += info.maxGood;
        p1Cost += info.minCost;
        // 可能性2，arr[index]，变成1
        int p2Value = 0;
        int p2Cost = arr[index] == 1 ? 0 : 1;
        info = process(arr, index + 1, pre, 1);
        p2Value += info.maxGood;
        p2Cost += info.minCost;
        // 可能性3，arr[index]，变成2
        int p3Value = prepre == 0 && pre == 1 ? 1 : 0;
        int p3Cost = arr[index] == 2 ? 0 : 1;
        info = process(arr, index + 1, pre, 2);
        p3Value += info.maxGood;
        p3Cost += info.minCost;
        // 开始决策，选出三种可能性中的最优解
        int maxGood = 0;
        // 值相等看谁的修改次数小
        int minCost = Integer.MAX_VALUE;
        if (p1Value > maxGood) {
            maxGood = p1Value;
            minCost = p1Cost;
        } else if (p1Value == maxGood) {
            minCost = Math.min(minCost, p1Cost);
        }
        if (p2Value > maxGood) {
            maxGood = p2Value;
            minCost = p2Cost;
        } else if (p2Value == maxGood) {
            minCost = Math.min(minCost, p2Cost);
        }
        if (p3Value > maxGood) {
            maxGood = p3Value;
            minCost = p3Cost;
        } else if (p3Value == maxGood) {
            minCost = Math.min(minCost, p3Cost);
        }
        return new Info(maxGood, minCost);
    }

}
```
