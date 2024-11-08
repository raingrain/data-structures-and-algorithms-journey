# 巴什博奕（Bash Game）SG版

## 左老师课堂讲述

> - **Question**
>   - 一共有 `n` 颗石子，两个人轮流拿，每次可以拿 `1 ~ m` 颗石子，拿到最后一颗石子的人获胜，返回谁赢。

## Java

> - **SG函数**

```java
import java.util.*;

class Solution {

    // 发现结论去求解，时间复杂度O(1)
    // 充分研究了性质
    public static String bash1(int n, int m) {
        return n % (m + 1) != 0 ? "先手" : "后手";
    }

    // sg函数去求解，时间复杂度O(n*m)
    // 不用研究性质
    // 其实把sg表打印之后，也可以发现性质，也就是打表找规律
    public static String bash2(int n, int m) {
        int[] sg = new int[n + 1];
        boolean[] appear = new boolean[m + 1];
        for (int i = 1; i <= n; i++) {
            Arrays.fill(appear, false);
            for (int j = 1; j <= m && i - j >= 0; j++) {
                appear[sg[i - j]] = true;
            }
            for (int s = 0; s <= m; s++) {
                if (!appear[s]) {
                    sg[i] = s;
                    break;
                }
            }
        }

//  System.out.println("打印 n = " + n + ", m = " + m + " 的sg表");
//  for (int i = 0; i <= n; i++) {
//   System.out.println("sg(" + i + ") : " + sg[i]);
//  }

        return sg[n] != 0 ? "先手" : "后手";
    }

    // 为了验证
    public static void main(String[] args) {
        int V = 1000;
        int testTimes = 10000;
        System.out.println("测试开始");
        for (int i = 0; i < testTimes; i++) {
            int n = (int) (Math.random() * V);
            int m = (int) (Math.random() * V);
            String ans1 = bash1(n, m);
            String ans2 = bash2(n, m);
            if (!ans1.equals(ans2)) {
                System.out.println("出错了!");
            }
        }
        System.out.println("测试结束");

        int n = 100;
        int m = 6;
        bash2(n, m);
    }

}
```
