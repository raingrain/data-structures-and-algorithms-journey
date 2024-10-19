# Flowerpot S

## [P2698 [USACO12MAR] Flowerpot S](https://www.luogu.com.cn/problem/P2698)

> - ***Question***
>   - 老板需要你帮忙浇花。给出 `N` 滴水的坐标， `y` 表示水滴的高度， `x` 表示它下落到 `x` 轴的位置。每滴水以每秒 `1` 个单位长度的速度下落。你需要把花盆放在 `x` 轴上的某个位置，使得从被花盆接着的第 `1` 滴水开始，到被花盆接着的最后 `1` 滴水结束，之间的时间差至少为 `D`。我们认为，只要水滴落到 `x` 轴上，与花盆的边沿对齐，就认为被接住。给出 `N` 滴水的坐标和 `D` 的大小，请算出最小的花盆的宽度 `W` 。
>   - ***输入描述***
>     - 第一行 `2` 个整数 `N` 和 `D` 。接下来 `N` 行每行 `2` 个整数，表示水滴的坐标 `(x, y)` 。
>   - ***输出描述***
>     - 仅一行 `1` 个整数，表示最小的花盆的宽度。如果无法构造出足够宽的花盆，使得在 `D` 单位的时间接住满足要求的水滴，则输出 `-1`。
>   - ***tips:***
>     - `1 <= N <= 10^5`
>     - `1 <= D <= 10^6`
>     - `0 <= x, y <= 10^6`

## Java

> - ***滑动窗口***
>   - 求使得最大值减去最小值大于指定数值的最长子数组长度。

```java
import java.io.*;
import java.util.Arrays;

public class Main {

    public static int MAXN = 100005;

    public static int[][] arr = new int[MAXN][2];

    public static int n, d;

    public static int[] maxDeque = new int[MAXN];

    public static int[] minDeque = new int[MAXN];

    public static int maxh, maxt, minh, mint;

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            n = (int) in.nval;
            in.nextToken();
            d = (int) in.nval;
            for (int i = 0; i < n; i++) {
                in.nextToken();
                arr[i][0] = (int) in.nval;
                in.nextToken();
                arr[i][1] = (int) in.nval;
            }
            int ans = compute();
            out.println(ans == Integer.MAX_VALUE ? -1 : ans);
        }
        out.flush();
        out.close();
        br.close();
    }

    public static int compute() {
        // arr[0...n-1][2]: x(0), 高度(1)
        // 所有水滴根据x排序，谁小谁在前
        Arrays.sort(arr, 0, n, (a, b) -> a[0] - b[0]);
        maxh = maxt = minh = mint = 0;
        int ans = Integer.MAX_VALUE;
        for (int l = 0, r = 0; l < n; l++) {
            // [l,r) : 水滴的编号
            // l : 当前花盘的左边界，arr[l][0]
            while (!ok() && r < n) {
                push(r++);
            }
            if (ok()) {
                ans = Math.min(ans, arr[r - 1][0] - arr[l][0]);
            }
            pop(l);
        }
        return ans;
    }

    // 当前窗口 最大值 - 最小值 是不是>=d
    public static boolean ok() {
        int max = maxh < maxt ? arr[maxDeque[maxh]][1] : 0;
        int min = minh < mint ? arr[minDeque[minh]][1] : 0;
        return max - min >= d;
    }

    public static void push(int r) {
        while (maxh < maxt && arr[maxDeque[maxt - 1]][1] <= arr[r][1]) {
            maxt--;
        }
        maxDeque[maxt++] = r;
        while (minh < mint && arr[minDeque[mint - 1]][1] >= arr[r][1]) {
            mint--;
        }
        minDeque[mint++] = r;
    }

    public static void pop(int l) {
        if (maxh < maxt && maxDeque[maxh] == l) {
            maxh++;
        }
        if (minh < mint && minDeque[minh] == l) {
            minh++;
        }
    }

}
```
