# 威佐夫博弈（Wythoff Game）模版

## [P2252 [SHOI2002] 取石子游戏|【模板】威佐夫博弈](https://www.luogu.com.cn/problem/P2252)

> - ***Question***
>   - 有两堆石子，数量任意，可以不同。游戏开始由两个人轮流取石子。游戏规定，每次有两种不同的取法：一是可以在任意的一堆中取走任意多的石子；二是可以在两堆中同时取走相同数量的石子。最后把石子全部取完者为胜者。现在给出初始的两堆石子的数目，你先取，假设双方都采取最好的策略，问最后你是胜者还是败者。
>   - ***输入描述***
>     - 输入共一行。
>     - 第一行共两个数 `a, b` ，表示石子的初始情况。
>   - ***输出描述***
>     - 输出共一行。
>     - 第一行为一个数字 `1, 0` 或 `-1` ，如果最后你是胜利者则为 `1` ；若失败则为 `0` ；若结果无法确定则为 `-1` 。
>   - ***tips:***
>     - `a, b <= 10^9`

---

## *Java*

> - ***黄金分割***

```java
import java.io.*;

public class Main {

    // 黄金分割比例
    public static double split = (Math.sqrt(5.0) + 1.0) / 2.0;

    public static int a, b;

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            a = (int) in.nval;
            in.nextToken();
            b = (int) in.nval;
            out.println(compute());
        }
        out.flush();
        out.close();
        br.close();
    }

    public static int compute() {
        int min = Math.min(a, b);
        int max = Math.max(a, b);
        // 威佐夫博弈
        // 小 != (大 - 小) * 黄金分割比例，先手赢
        // 小 == (大 - 小) * 黄金分割比例，后手赢
        // 要向下取整
        if (min != (int) (split * (max - min))) {
            return 1;
        } else {
            return 0;
        }
    }

}
```
