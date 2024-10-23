# 巴什博弈及其拓展（Bash Game）

## 左老师课堂讲述

> - **Question 1**
>   - 一共有 `n` 颗石子，两个人轮流拿，每次可以拿 `1 ~ m` 颗石子，拿到最后一颗石子的人获胜，返回谁赢。

## [P4018 Roy&October之取石子](https://www.luogu.com.cn/problem/P4018)

> - **Question 2**
>   - 游戏规则是这样的：共有 `n` 个石子，两人每次都只能取 `p^k` 个， `p` 为质数， `k` 为自然数，且 `p^k` 小于等于当前剩余石子数，谁取走最后一个石子，谁就赢了。
>   - 现在 `October` 先取（ `Roy` 后取），问她有没有必胜策略。
>   - 若她有必胜策略，输出一行 `October wins!` ；否则输出一行 `Roy wins!` 。
>   - **输入描述**
>     - 第一行一个正整数 `T` ，表示测试点组数。
>     - 第 `2` 行到第 `T + 1` 行，一行一个正整数 `n` ，表示石子个数。
>   - **输出描述**
>     - `T` 行，每行分别为 `October wins!` 或 `Roy wins!` 。
>   - **Tips**
>     - `1 <= n <= 5 * 10^7`
>     - `1 <= T <= 10^5`

## Java

> - **Question 1: 巴什博弈模版**
>   - 只要 `n` 不是 `m + 1` 的整数倍，先手就可以让后手一直面对要在 `m + 1` 的整数倍颗石子中拿的情况，最后时会剩下 `m + 1` 颗石子，后手怎么样都拿不完，而最后一颗石子在下一轮一定被先手拿走。而当 `n` 是 `m + 1` 的整数倍时，反之后手必胜。

```java
class Solution {

    // 动态规划进行所有尝试
    // 为了验证
    public static int MAXN = 1001;

    public static String[][] dp = new String[MAXN][MAXN];

    public static String bashGame1(int n, int m) {
        if (n == 0) {
            return "后手";
        }
        if (dp[n][m] != null) {
            return dp[n][m];
        }
        String ans = "后手";
        for (int pick = 1; pick <= m; pick++) {
            if (bashGame1(n - pick, m).equals("后手")) {
                // 后续过程的赢家是后续过程的后手
                // 那就表示此时的先手，通过这个后续过程，能赢
                ans = "先手";
                break;
            }
        }
        dp[n][m] = ans;
        return ans;
    }

    // 正式方法
    public static String bashGame2(int n, int m) {
        return n % (m + 1) != 0 ? "先手" : "后手";
    }

    // 为了验证
    public static void main(String[] args) {
        int V = 500; // 需要比MAXN小
        int testTimes = 5000;
        System.out.println("测试开始");
        for (int i = 0; i < testTimes; i++) {
            int n = (int) (Math.random() * V);
            int m = (int) (Math.random() * V) + 1;
            String ans1 = bashGame1(n, m);
            String ans2 = bashGame2(n, m);
            if (!ans1.equals(ans2)) {
                System.out.println("出错了!");
            }
        }
        System.out.println("测试结束");
    }

}
```

> - **Question 2: 巴什博奕拓展**

```java
import java.io.*;

public class Main {

    public static int t, n;

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        t = (int) in.nval;
        for (int i = 0; i < t; i++) {
            in.nextToken();
            n = (int) in.nval;
            out.println(compute(n));
        }
        out.flush();
        out.close();
        br.close();
    }

    // 两人能拿的石子一定不是6及6的整数倍，因为它一定不是质数的自然数次方，它有2和3两个因子
    // 同理，找到一个不是可拿石子范围内的数字，石子总量是它的倍数后手赢，否则先手赢
    public static String compute(int n) {
        return n % 6 != 0 ? "October wins!" : "Roy wins!";
    }

}
```
