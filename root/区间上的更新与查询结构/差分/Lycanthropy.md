# Lycanthropy

## [P5026 Lycanthropy](https://www.luogu.com.cn/problem/P5026)

> - ***Question***
>   - 我们把山顶上的湖泊看作一条长度为 `m` 的直线，一开始水深都在水平线上，我们视作此时的水深为 `0` 。接下来，在一瞬间，小正方形的朋友们跳起并扎入水中，导致在入水点的水降低而远离入水点的水升高，注意两个朋友可能在同一地点入水。小正方形的每个朋友有一个体积数值 `v` ，当体积为 `v` 的一个朋友跳入水中，我们设入水点为 `i` ，将会导致 `i - v + 1` 到 `i` 的水位依次降低 `1, 2, ..., v` 。同样地，第 `i` 到 `i + v - 1` 的水位会依次降低 `v, v - 1, ..., 1` 。相对应地， `i - v` 的水位不变， `i - v - 1` 到 `i - 2 ∗ v` 水位依次增加 `1, 2, ..., v` ， `i - 2 ∗ v` 到 `i - 3 ∗ v + 1` 水位依次增加 `v, v - 1, ..., 1` 。同样， `i + v` 水位不变， `i + v + 1` 到 `i + 2 ∗ v` 水位增加 `1, 2, ..., v` ， `i + 2 ∗ v` 到 `i + 3 ∗ v - 1` 水位依次增加 `v, v - 1, ..., 1` 。现在小正方形想要穿过这个湖，他想要知道在这 `n` 个朋友跳入水中后湖上每个节点的水位，你能帮帮它吗？
>   - ***输入描述***
>     - 第一行为两个整数 `n, m` ，表示朋友的数目与湖泊的宽度。接下来 `n` 行，一行两个整数 `v, x` ，表示第 `i + 1` 个朋友的体积与入水点。
>   - ***输出描述***
>     - 一行 `m` 个整数，第 `i` 个整数表示 `i` 号位的水深。
>   - ***tips:***
>     - `n, m <= 10^6`
>     - `1 <= v <= 10000`
>     - `1 <= x <= m`

---

## *Java*

> - ***一维等差数列差分***

```java
import java.io.*;

public class Main {

    // 题目说了m <= 10^6，代表湖泊宽度
    // 这就是MAXN的含义，湖泊最大宽度的极限
    public static int MAXN = 1000001;

    // 数值保护，因为题目中v最大是10000
    // 所以左侧影响最远的位置到达了x - 3 * v + 1
    // 所以右侧影响最远的位置到达了x + 3 * v - 1
    // x如果是正式的位置(1~m)，那么左、右侧可能超过正式位置差不多30000的规模
    // 这就是OFFSET的含义
    public static int OFFSET = 30001;

    // 湖泊宽度是MAXN，是正式位置的范围
    // 左、右侧可能超过正式位置差不多OFFSET的规模
    // 所以准备一个长度为OFFSET + MAXN + OFFSET的数组
    // 这样一来，左侧影响最远的位置...右侧影响最远的位置，
    // 都可以被arr中的下标表示出来，就省去了很多越界讨论
    // 详细解释看set方法的注释
    public static int[] arr = new int[OFFSET + MAXN + OFFSET];

    public static int n, m;

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            // n有多少个人落水，每个人落水意味着四个等差数列操作
            n = (int) in.nval;
            in.nextToken();
            // 一共有多少位置，1~m个位置，最终要打印每个位置的水位
            m = (int) in.nval;
            for (int i = 0, v, x; i < n; i++) {
                in.nextToken();
                v = (int) in.nval;
                in.nextToken();
                x = (int) in.nval;
                // v体积的朋友，在x处落水，修改差分数组
                fall(v, x);
            }
            // 生成最终的水位数组
            build();
            // 开始收集答案
            // 0...OFFSET这些位置是辅助位置，为了防止越界设计的
            // 从OFFSET+1开始往下数m个，才是正式的位置1...m
            // 打印这些位置，就是返回正式位置1...m的水位
            int start = OFFSET + 1;
            out.print(arr[start++]);
            for (int i = 2; i <= m; i++) {
                out.print(" " + arr[start++]);
            }
            out.println();
        }
        out.flush();
        out.close();
        br.close();
    }

    public static void fall(int v, int x) {
        set(x - 3 * v + 1, x - 2 * v, 1, v, 1);
        set(x - 2 * v + 1, x, v - 1, -v, -1);
        set(x + 1, x + 2 * v, -v + 1, v, 1);
        set(x + 2 * v + 1, x + 3 * v - 1, v - 1, 1, -1);
    }

    public static void set(int l, int r, int s, int e, int d) {
        // 为了防止x - 3 * v + 1出现负数下标，进而有很多很烦的边界讨论
        // 所以任何位置，都加上一个较大的数字(OFFSET)
        // 这样一来，所有下标就都在0以上了，省去了大量边界讨论
        // 这就是为什么arr在初始化的时候要准备OFFSET + MAXN + OFFSET这么多的空间
        arr[l + OFFSET] += s;
        arr[l + 1 + OFFSET] += d - s;
        arr[r + 1 + OFFSET] -= d + e;
        arr[r + 2 + OFFSET] += e;
    }

    public static void build() {
        for (int i = 1; i <= m + OFFSET; i++) {
            arr[i] += arr[i - 1];
        }
        for (int i = 1; i <= m + OFFSET; i++) {
            arr[i] += arr[i - 1];
        }
    }

}
```

---

> ***last change: 2023/10/11***

---
