# windy 数

## [P2657 [SCOI2009] windy 数](https://www.luogu.com.cn/problem/P2657)

> - ***Question***
>   - 不含前导零且相邻两个数字之差至少为 `2` 的正整数被称为 `windy` 数。在 `[a, b]` 中总共有多少个 `windy` 数？
>   - ***输入描述***
>     - 输入只有一行两个整数，分别表示 `a` 和 `b` 。
>   - ***输出描述***
>     - 输出一行一个整数表示答案。
>   - ***tips:***
>     - `1 <= a <= b <= 2 * 10^9`

---

## *Java*

> - ***数位DP***

```java
import java.io.*;

public class Main {

    public static int MAXLEN = 11;

    public static int[][][] dp = new int[MAXLEN][11][2];

    public static void build(int len) {
        for (int i = 0; i <= len; i++) {
            for (int j = 0; j <= 10; j++) {
                dp[i][j][0] = -1;
                dp[i][j][1] = -1;
            }
        }
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            int a = (int) in.nval;
            in.nextToken();
            int b = (int) in.nval;
            out.println(compute(a, b));
        }
        out.flush();
        out.close();
        br.close();
    }

    // 计算0-b上符合题意的数字个数，减去0-a-1上的，就是a-b上的
    public static int compute(int a, int b) {
        return cnt(b) - cnt(a - 1);
    }

    // 求0~num范围上，windy数的个数
    public static int cnt(int num) {
        // 特判
        if (num == 0) {
            return 1;
        }
        int len = 1;
        int offset = 1;
        int tmp = num / 10;
        while (tmp > 0) {
            len++;
            offset *= 10;
            tmp /= 10;
        }
        build(len);
        return f(num, offset, len, 10, 0);
    }

    // offset完全由len决定，为了方便提取num中某一位数字（上节课内容）
    // 从num的高位开始，还剩下len位没有决定
    // 前一位的数字是pre，如果pre == 10，表示从来没有选择过数字
    // 如果之前的位已经确定比num小，那么free == 1，表示接下的数字可以自由选择
    // 如果之前的位和num一样，那么free == 0，表示接下的数字不能大于num当前位的数字
    // 返回<=num的windy数有多少个
    public static int f(int num, int offset, int len, int pre, int free) {
        if (len == 0) {
            // 都没选，认为是0，选了，到达最终，也是一种选择
            return 1;
        }
        if (dp[len][pre][free] != -1) {
            return dp[len][pre][free];
        }
        int cur = num / offset % 10;
        int ans = 0;
        if (free == 0) {
            if (pre == 10) {
                // 之前的位和num一样，此时不能随意选择数字
                // 也从来没有选择过数字
                // 就表示：来到的是num的最高位
                // 一个数字也不要，位数少，free变1
                ans += f(num, offset / 10, len - 1, 10, 1);
                // 要比最高位小的数字，free变1
                for (int i = 1; i < cur; i++) {
                    ans += f(num, offset / 10, len - 1, i, 1);
                }
                // 要等于最高位的数字，free为0
                ans += f(num, offset / 10, len - 1, cur, 0);
            } else {
                // 之前的位和num一样，此时不能随意选择数字，
                // 之前选择过数字pre
                for (int i = 0; i <= 9; i++) {
                    // 满足相邻数差大于2
                    if (i <= pre - 2 || i >= pre + 2) {
                        // 还要满足小于等于当前位
                        if (i < cur) {
                            // 选小于当前位且能满足条件的数字，free为1
                            ans += f(num, offset / 10, len - 1, i, 1);
                        } else if (i == cur) {
                            // 当前位一样的数字可以同时满足两个条件，free为0
                            ans += f(num, offset / 10, len - 1, cur, 0);
                        }
                    }
                }
            }
        } else {
            if (pre == 10) {
                // free == 1，可以自由选择数字，前面的状况 < num
                // 从来没有选择过数字
                // 还是可以不选择数字
                ans += f(num, offset / 10, len - 1, 10, 1);
                // 任选
                for (int i = 1; i <= 9; i++) {
                    ans += f(num, offset / 10, len - 1, i, 1);
                }
            } else {
                // free == 1，可以自由选择数字，前面的状况 < num
                // 选择过数字pre
                for (int i = 0; i <= 9; i++) {
                    // 任选时满足差大于2
                    if (i <= pre - 2 || i >= pre + 2) {
                        ans += f(num, offset / 10, len - 1, i, 1);
                    }
                }
            }
        }
        dp[len][pre][free] = ans;
        return ans;
    }

}
```

---

> ***last change: 2024/1/17***

---
