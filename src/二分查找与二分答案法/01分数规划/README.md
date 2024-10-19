# 01分数规划

## [P10505 Dropping Test](https://www.luogu.com.cn/problem/P10505)

> - ***Question***
>   - 给定 `n` 个数据，每个数据有 `(a, b)` 两个值，并且没有负数，即 `(a1, b1), (a2, b2), ..., (an, bn)` 。其中选出 `k` 个数据，希望让 `k个a的和 / k个b的和` ，这个比值尽量大，返回最大的比值。
>   - ***tips:***
>     - `1 <= n <= 100`
>     - `0 <= a、b <= 10^9`

---

## *Java*

> - ***01规划模板题***

```java
// 错误的贪心：选择a/b最大的前k个数据，反例：(5, 5) (0, 1) (2, 6)。

// 暴力方法：2的n次方，每个数据都枚举要和不要两种选择。

// 计算机里关于小数的精度是有限的或者具体题目要求返回的比值精度也是规定好的，那么此时没有必要精确求解，可以用二分答案法来逼近求解，保证规定精度内正确即可。

// 01分数规划的核心：数据转化成结余表达式的形式，当确定一个比值x，就在最优决策下，计算结余和，最终希望获得在最优决策下，当结余和最接近0时，x的值。

// 手段：利用单调性进行二分。

// 二分答案法找到最大比值的过程，数据这么来看：
// a1 - x * b1 = 结余1
// a2 - x * b2 = 结余2
// a3 - x * b3 = 结余3

// 规定精度的最小单位sml，找到x的可能范围[l, r]，然后二分逻辑如下：
// 首先令x = (l + r) / 2，计算每一个结余，然后选出结余最大的前k名，然后加起来看看累加和
// 1) 如果 结余累加和 >= 0，答案更新成此时的x，然后去右侧二分，范围变成[x + sml, r]
// 2) 如果 结余累加和 <  0，答案不更新，然后去左侧二分，范围变成[l, x - sml]
// 3) 如果发现范围[l,r]的大小 <= sml，过程停止，返回答案即可

// 时间复杂度O(n * logn * logv)


import java.io.*;
import java.util.Arrays;
import java.util.Comparator;

public class Main {

    public static int MAXN = 1001;

    public static double sml = 1e-6;

    // arr[i][0] = i号数据的a
    // arr[i][1] = i号数据的b
    // arr[i][2] = i号数据的结余，a - x * b
    public static double[][] arr = new double[MAXN][3];

    public static int n, k;

    public static boolean check(double x) {
        // x固定的情况下，得到所有数据的结余
        for (int i = 1; i <= n; i++) {
            arr[i][2] = arr[i][0] - x * arr[i][1];
        }
        // 结余从大到小排序
        Arrays.sort(arr, 1, n + 1, new MyComparator());
        // 最大k个结余的累加和
        double sum = 0;
        for (int i = 1; i <= k; i++) {
            sum += arr[i][2];
        }
        return sum >= 0;
    }

    // poj平台java版本较老，不支持Lambda表达式方式的比较器，需要自己定义
    public static class MyComparator implements Comparator<double[]> {

        @Override
        public int compare(double[] o1, double[] o2) {
            return o1[2] >= o2[2] ? -1 : 1;
        }

    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        n = (int) in.nval;
        in.nextToken();
        k = (int) in.nval;
        while (n != 0 || k != 0) {
            // 题目要求舍弃掉k个，其实就是挑选n-k个
            k = n - k;
            for (int i = 1; i <= n; i++) {
                in.nextToken();
                arr[i][0] = in.nval;
            }
            for (int i = 1; i <= n; i++) {
                in.nextToken();
                arr[i][1] = in.nval;
            }
            double l = 0, r = 0, x;
            for (int i = 1; i <= n; i++) {
                r += arr[i][0];
            }
            double ans = 0;
            while (l < r && r - l >= sml) {
                x = (l + r) / 2;
                if (check(x)) {
                    ans = x;
                    l = x + sml;
                } else {
                    r = x - sml;
                }
            }
            out.println((int) (100 * (ans + 0.005)));
            in.nextToken();
            n = (int) in.nval;
            in.nextToken();
            k = (int) in.nval;
        }
        out.flush();
        out.close();
        br.close();
    }

}
```
