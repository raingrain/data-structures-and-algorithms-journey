# 多重背包模版

## [P1776 宝物筛选](https://www.luogu.com.cn/problem/P1776)

> - **Question**
>   - 终于，破解了千年的难题。小FF找到了王室的宝物室，里面堆满了无数价值连城的宝物。
>   - 这下小FF可发财了，嘎嘎。但是这里的宝物实在是太多了，小 FF 的采集车似乎装不下那么多宝物。看来小FF只能含泪舍弃其中的一部分宝物了。
>   - 小FF对洞穴里的宝物进行了整理，他发现每样宝物都有一件或者多件。他粗略估算了下每样宝物的价值，之后开始了宝物筛选工作：小FF有一个最大载重为 `W` 的采集车，洞穴里总共有 `n` 种宝物，每种宝物的价值为 `vi` ，重量为 `wi` ，每种宝物有 `mi` 件。小FF希望在采集车不超载的前提下，选择一些宝物装进采集车，使得它们的价值和最大。
>   - **输入描述**
>     - 第一行为一个整数 `n` 和 `W` ，分别表示宝物种数和采集车的最大载重。接下来 `n` 行每行三个整数 `vi, wi, mi` 。
>   - **输出描述**
>     - 输出仅一个整数，表示在采集车不超载的情况下收集的宝物的最大价值。
>   - **Tips**
>     - `n <= sum(mi) <= 10^5`
>     - `0 <= W <= 4 * 10^4`
>     - `1 <= n <= 100`

## Java

> - **动态规划**
>   - ![images](images/多重背包不进行枚举优化模版.png)

```java
import java.io.*;

public class Main {

    public static int MAXN = 101;

    public static int MAXW = 40001;

    public static int[] v = new int[MAXN];

    public static int[] w = new int[MAXN];

    public static int[] c = new int[MAXN];

    public static int[] dp = new int[MAXW];

    public static int n, t;

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            n = (int) in.nval;
            in.nextToken();
            t = (int) in.nval;
            for (int i = 1; i <= n; i++) {
                in.nextToken();
                v[i] = (int) in.nval;
                in.nextToken();
                w[i] = (int) in.nval;
                in.nextToken();
                c[i] = (int) in.nval;
            }
            out.println(compute2());
        }
        out.flush();
        out.close();
        br.close();
    }

    // 严格位置依赖的动态规划
    // 时间复杂度O(n * t * 每种商品的平均个数)
    public static int compute1() {
        // dp[0][....] = 0，表示没有货物的情况下，背包容量不管是多少，最大价值都是0
        int[][] dp = new int[n + 1][t + 1];
        for (int i = 1; i <= n; i++) {
            for (int j = 0; j <= t; j++) {
                dp[i][j] = dp[i - 1][j];
                for (int k = 1; k <= c[i] && w[i] * k <= j; k++) {
                    dp[i][j] = Math.max(dp[i][j], dp[i - 1][j - k * w[i]] + k * v[i]);
                }
            }
        }
        return dp[n][t];
    }

    // 空间压缩
    // 部分测试用例超时
    // 因为没有优化枚举
    // 时间复杂度O(n * t * 每种商品的平均个数)
    public static int compute2() {
        for (int i = 1; i <= n; i++) {
            for (int j = t; j >= 0; j--) {
                for (int k = 1; k <= c[i] && w[i] * k <= j; k++) {
                    dp[j] = Math.max(dp[j], dp[j - k * w[i]] + k * v[i]);
                }
            }
        }
        return dp[t];
    }

}
```

> - **利用二进制转01背包**
>   - 可能有重复计算，但不会漏掉任何一种情况，也不会多算。

```java
import java.io.*;
import java.util.Arrays;

public class Main {

    public static int MAXN = 1001;

    public static int MAXW = 40001;

    // 把每一种货物根据个数做二进制分组，去生成衍生商品
    // 衍生出来的每一种商品，价值放入v、重量放入w
    public static int[] v = new int[MAXN];

    public static int[] w = new int[MAXN];

    public static int[] dp = new int[MAXW];

    public static int n, t, m;

    // 时间复杂度O(t * (log(第1种商品的个数) + log(第2种商品的个数) + ... + log(第n种商品的个数)))
    // 对每一种商品的个数取log，所以时间复杂度虽然大于O(n * t)，但也不会大多少
    // 多重背包最常用的方式
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            n = (int) in.nval;
            in.nextToken();
            t = (int) in.nval;
            m = 0;
            for (int i = 1, value, weight, cnt; i <= n; i++) {
                in.nextToken();
                value = (int) in.nval;
                in.nextToken();
                weight = (int) in.nval;
                in.nextToken();
                cnt = (int) in.nval;
                // 整个文件最重要的逻辑 : 二进制分组
                // 一般都使用这种技巧，这段代码非常重要
                // 虽然时间复杂度不如单调队列优化的版本
                // 但是好写，而且即便是比赛，时间复杂度也达标
                // 二进制分组的时间复杂度为O(log cnt)
                for (int k = 1; k <= cnt; k <<= 1) {
                    v[++m] = k * value;
                    w[m] = k * weight;
                    cnt -= k;
                }
                if (cnt > 0) {
                    v[++m] = cnt * value;
                    w[m] = cnt * weight;
                }
            }
            out.println(compute());
        }
        out.flush();
        out.close();
        br.close();
    }

    // 01背包的空间压缩代码(模版)
    public static int compute() {
        Arrays.fill(dp, 0, t + 1, 0);
        for (int i = 1; i <= m; i++) {
            for (int j = t; j >= w[i]; j--) {
                dp[j] = Math.max(dp[j], dp[j - w[i]] + v[i]);
            }
        }
        return dp[t];
    }

}
```

> - **单调队列优化**

```java
import java.io.*;

public class Main {

    public static int MAXN = 101;

    public static int MAXW = 40001;

    public static int[] v = new int[MAXN];

    public static int[] w = new int[MAXN];

    public static int[] c = new int[MAXN];

    public static int[] dp = new int[MAXW];

    public static int[] queue = new int[MAXW];

    public static int l, r;

    public static int n, t;

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            n = (int) in.nval;
            in.nextToken();
            t = (int) in.nval;
            for (int i = 1; i <= n; i++) {
                in.nextToken();
                v[i] = (int) in.nval;
                in.nextToken();
                w[i] = (int) in.nval;
                in.nextToken();
                c[i] = (int) in.nval;
            }
            out.println(compute2());
        }
        out.flush();
        out.close();
        br.close();
    }

    // 严格位置依赖的动态规划 + 单调队列优化枚举
    public static int compute1() {
        int[][] dp = new int[n + 1][t + 1];
        for (int i = 1; i <= n; i++) {
            for (int mod = 0; mod <= Math.min(t, w[i] - 1); mod++) {
                l = r = 0;
                for (int j = mod; j <= t; j += w[i]) {
                    // dp[i-1][j]
                    // dp[i][j]
                    // queue[r - 1] -> x
                    // j -> y
                    while (l < r && dp[i - 1][queue[r - 1]] + inc(j - queue[r - 1], i) <= dp[i - 1][j]) {
                        // queue[r-1]是队列尾部的列号 vs j这个列号
                        // 指标之间pk
                        r--;
                    }
                    queue[r++] = j;
                    if (queue[l] == j - w[i] * (c[i] + 1)) {
                        // 检查单调队列最左的列号，是否过期
                        // 比如
                        // i号物品，重量为3，个数4
                        // queue[l]是队列头部的列号，假设是2
                        // 当j == 17时，依赖的格子为dp[i-1][17、14、11、8、5]
                        // 所以此时头部的列号2，过期了，要弹出
                        l++;
                    }
                    // dp[i][j] = dp[i-1][拥有最强指标的列] + (j - 拥有最强指标的列) / i号物品重量 * i号物品价值
                    dp[i][j] = dp[i - 1][queue[l]] + inc(j - queue[l], i);
                }
            }
        }
        return dp[n][t];
    }

    // s的容量用来装i号商品，可以得到多少价值
    public static int inc(int s, int i) {
        return s / w[i] * v[i];
    }

    // 单调队列优化枚举 + 空间压缩
    // 理解了原理之后，这个函数就没有理解难度了
    // 难度来自实现和注意边界条件，可以自己尝试一下
    public static int compute2() {
        for (int i = 1; i <= n; i++) {
            for (int mod = 0; mod <= Math.min(t, w[i] - 1); mod++) {
                // 因为空间压缩时，关于j的遍历是从右往左，而不是从左往右
                // 所以先让dp[i-1][...右侧的几个位置]进入窗口
                l = r = 0;
                for (int j = t - mod, k = 0; j >= 0 && k <= c[i]; j -= w[i], k++) {
                    while (l < r && dp[j] + inc(queue[r - 1] - j, i) >= dp[queue[r - 1]]) {
                        r--;
                    }
                    queue[r++] = j;
                }
                // 然后j开始从右往左遍历
                // 注意，因为j从右往左遍历，所以：
                // 更靠右的j位置更早进入窗口
                // 更靠左的j位置更晚进入窗口
                for (int j = t - mod; j >= 0; j -= w[i]) {
                    // 来到j，计算dp[i][j]的值，做了空间压缩，所以去更新dp[j]
                    dp[j] = dp[queue[l]] + inc(j - queue[l], i);
                    // 求解完dp[j]
                    // 接下来要去求解dp[j-w[i]]了(根据余数分组)
                    // 所以看看窗口最左的下标是不是j(其实代表dp[i-1][j]的值]
                    // 是的话，说明最左下标过期了，要弹出
                    if (queue[l] == j) {
                        l++;
                    }
                    // 最后
                    // 因为接下来要去求解dp[j-w[i]]了
                    // 所以新的dp[i-1][enter]要进入窗口了
                    // 用单调队列的更新方式让其进入
                    int enter = j - w[i] * (c[i] + 1);
                    if (enter >= 0) {
                        while (l < r && dp[enter] + inc(queue[r - 1] - enter, i) >= dp[queue[r - 1]]) {
                            r--;
                        }
                        queue[r++] = enter;
                    }
                }
            }
        }
        return dp[t];
    }

}
```
