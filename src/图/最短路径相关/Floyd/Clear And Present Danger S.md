# Clear And Present Danger S

## [P2910 [USACO08OPEN] Clear And Present Danger S](https://www.luogu.com.cn/problem/P2910)

> - ***Question***
>   - 农夫约翰正驾驶一条小艇在牛勒比海上航行。海上有 `N (1 <= N <= 100)` 个岛屿，用 `1` 到 `N` 编号．约翰从 `1` 号小岛出发，最后到达 `N` 号小岛。
>   - 一张藏宝图上说，如果他的路程上经过的小岛依次出现了 `A1, A2, ..., AM (2 <= M <= 10000)` 这样的序列（不一定相邻），那他最终就能找到古老的宝藏。但是，由于牛勒比海有海盗出没。约翰知道任意两个岛屿之间的航线上海盗出没的概率，他用一个危险指数 `Dij (0 <= Dij <= 100000)` 来描述。他希望他的寻宝活动经过的航线危险指数之和最小。那么，在找到宝藏的前提下，这个最小的危险指数是多少呢？
>   - ***输入描述***
>     - 第一行：两个用空格隔开的正整数 `N` 和 `M` 。
>     - 第二到第 `M + 1` 行：第 `i + 1` 行用一个整数 `Ai` 表示 `FJ` 必须经过的第 `i` 个岛屿。
>     - 第 `M + 2` 到第 `N + M + 1` 行：第 `i + M + 1` 行包含 `N` 个用空格隔开的非负整数分别表示 `i` 号小岛到第 `1...N` 号小岛的航线各自的危险指数。保证第 `i` 个数是 `0` 。
>   - ***输出描述***
>     - `FJ` 在找到宝藏的前提下经过的航线的危险指数之和的最小值。

## Java

> - ***Floyd***

```java
import java.io.*;

public class Main {

    public static int MAXN = 101;

    public static int MAXM = 10001;

    public static int[] path = new int[MAXM];

    public static int[][] distance = new int[MAXN][MAXN];

    public static int n, m, ans;

    // 初始时设置任意两点之间的最短距离为无穷大，表示任何路不存在
    public static void build() {
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                distance[i][j] = Integer.MAX_VALUE;
            }
        }
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            n = (int) in.nval;
            in.nextToken();
            m = (int) in.nval;
            for (int i = 0; i < m; i++) {
                in.nextToken();
                path[i] = (int) in.nval - 1;
            }
            // 这道题给的图是邻接矩阵的形式
            // 任意两点之间的边权都会给定
            // 所以显得distance初始化不太必要
            // 但是一般情况下，distance初始化一定要做
            build();
            for (int i = 0; i < n; i++) {
                for (int j = 0; j < n; j++) {
                    in.nextToken();
                    distance[i][j] = (int) in.nval;
                }
            }
            floyd();
            ans = 0;
            for (int i = 1; i < m; i++) {
                ans += distance[path[i - 1]][path[i]];
            }
            out.println(ans);
        }
        out.flush();
        out.close();
        br.close();
    }

    public static void floyd() {
        // O(N^3)的过程 O(N^2)空间复杂度
        // 枚举每个跳板
        // 注意，跳板要最先枚举！跳板要最先枚举！跳板要最先枚举！
        for (int bridge = 0; bridge < n; bridge++) { // 跳板
            for (int i = 0; i < n; i++) {
                for (int j = 0; j < n; j++) {
                    if (distance[i][bridge] != Integer.MAX_VALUE && distance[bridge][j] != Integer.MAX_VALUE && distance[i][j] > distance[i][bridge] + distance[bridge][j]) {
                        // 首先要能从起点到达跳板且能从跳板到达要去的点
                        distance[i][j] = distance[i][bridge] + distance[bridge][j];
                    }
                }
            }
        }
    }

}
```
