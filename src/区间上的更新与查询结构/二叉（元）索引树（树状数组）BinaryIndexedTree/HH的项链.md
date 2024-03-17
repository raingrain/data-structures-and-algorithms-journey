# HH的项链

## [P1972 [SDOI2009] HH的项链](https://www.luogu.com.cn/problem/P1972)

> - ***Question***
>   - HH有一串由各种漂亮的贝壳组成的项链。HH相信不同的贝壳会带来好运，所以每次散步完后，他都会随意取出一段贝壳，思考它们所表达的含义。HH不断地收集新的贝壳，因此，他的项链变得越来越长。
>   - 有一天，他突然提出了一个问题：某一段贝壳中，包含了多少种不同的贝壳？这个问题很难回答，因为项链实在是太长了。于是，他只好求助睿智的你，来解决这个问题。
>   - ***输入描述***
>     - 一行一个正整数 `n` ，表示项链长度。
>     - 第二行 `n` 个正整数 `ai` ，表示项链中第 `i` 个贝壳的种类。
>     - 第三行一个整数 `m` ，表示HH询问的个数。
>     - 接下来 `m` 行，每行两个整数 `l, r` ，表示询问的区间。
>   - ***输出描述***
>     - 输出 `m` 行，每行一个整数，依次表示询问对应的答案。
>   - ***tips:***
>     - `1 <= n, m, ai <= 10^6`
>     - `1 <= l <= r <= n`

---

## *Java*

> - ***树状数组***

```java
import java.io.*;
import java.util.Arrays;

public class Main {

    public static int MAXN = 1000010;

    public static int[] arr = new int[MAXN];

    public static int[][] query = new int[MAXN][3];

    public static int[] ans = new int[MAXN];

    public static int[] map = new int[MAXN];

    public static int[] tree = new int[MAXN];

    public static int n, m;

    public static int lowbit(int i) {
        return i & -i;
    }

    public static void add(int i, int v) {
        while (i <= n) {
            tree[i] += v;
            i += lowbit(i);
        }
    }

    public static int sum(int i) {
        int ans = 0;
        while (i > 0) {
            ans += tree[i];
            i -= lowbit(i);
        }
        return ans;
    }

    public static int range(int l, int r) {
        return sum(r) - sum(l - 1);
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        n = (int) in.nval;
        for (int i = 1; i <= n; i++) {
            in.nextToken();
            arr[i] = (int) in.nval;
        }
        in.nextToken();
        m = (int) in.nval;
        for (int i = 1; i <= m; i++) {
            in.nextToken();
            query[i][0] = (int) in.nval;
            in.nextToken();
            query[i][1] = (int) in.nval;
            query[i][2] = i;
        }
        compute();
        for (int i = 1; i <= m; i++) {
            out.println(ans[i]);
        }
        out.flush();
        out.close();
        br.close();
    }

    public static void compute() {
        Arrays.sort(query, 1, m + 1, (a, b) -> a[1] - b[1]);
        for (int s = 1, q = 1, l, r, i; q <= m; q++) {
            r = query[q][1];
            for (; s <= r; s++) {
                int color = arr[s];
                if (map[color] != 0) {
                    add(map[color], -1);
                }
                add(s, 1);
                map[color] = s;
            }
            l = query[q][0];
            i = query[q][2];
            ans[i] = range(l, r);
        }
    }

}
```

---

> ***last change: 2024/3/17***

---
