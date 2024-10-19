# Balanced Lineup G

## [P2880 [USACO07JAN] Balanced Lineup G](https://www.luogu.com.cn/problem/P2880)

> - ***Question***
>   - For the daily milking, Farmer John's N cows (1 ≤ N ≤ 50,000) always line up in the same order. One day Farmer John decides to organize a game of Ultimate Frisbee with some of the cows. To keep things simple, he will take a contiguous range of cows from the milking lineup to play the game. However, for all the cows to have fun they should not differ too much in height.
>   - Farmer John has made a list of Q (1 ≤ Q ≤ 180,000) potential groups of cows and their heights (1 ≤ height ≤ 1,000,000). For each group, he wants your help to determine the difference in height between the shortest and the tallest cow in the group.
>   - ***输入描述***
>     - Line 1: Two space-separated integers, N and Q.
>     - Lines 2..N+1: Line i+1 contains a single integer that is the height of cow i
>     - Lines N+2..N+Q+1: Two integers A and B (1 ≤ A ≤ B ≤ N), representing the range of cows from A to B inclusive.
>   - ***输出描述***
>     - Lines 1..Q: Each line contains a single integer that is a response to a reply and indicates the difference in height between the tallest and shortest cow in the range.

## Java

> - ***ST表***
>   - 给定一个长度为 `n` 的数组 `arr` ，一共有 `m` 次查询，每次查询 `arr[l ~ r]` 上的最大值和最小值，每次查询只需要打印最大值-最小值的结果。

```java
import java.io.*;
import java.util.Arrays;

public class Main {

    public static int MAXN = 50001;

    // 2的15次方是<=50001且最接近的
    // 所以次方可能是0~15
    // 于是准备16长度够用了
    public static int LIMIT = 16;

    public static int[] arr = new int[MAXN];

    // log2[i] : 查询<=i情况下，最大的2的幂，是2的几次方
    public static int[] log2 = new int[MAXN];

    public static int[][] stmax = new int[MAXN][LIMIT];

    public static int[][] stmin = new int[MAXN][LIMIT];

    public static void build(int n) {
        log2[0] = -1;
        for (int i = 1; i <= n; i++) {
            log2[i] = log2[i >> 1] + 1;
            stmax[i][0] = arr[i];
            stmin[i][0] = arr[i];
        }
        for (int p = 1; p <= log2[n]; p++) {
            for (int i = 1; i + (1 << p) - 1 <= n; i++) {
                stmax[i][p] = Math.max(stmax[i][p - 1], stmax[i + (1 << (p - 1))][p - 1]);
                stmin[i][p] = Math.min(stmin[i][p - 1], stmin[i + (1 << (p - 1))][p - 1]);
            }
        }
    }

    public static int query(int l, int r) {
        int p = log2[r - l + 1];
        int a = Math.max(stmax[l][p], stmax[r - (1 << p) + 1][p]);
        int b = Math.min(stmin[l][p], stmin[r - (1 << p) + 1][p]);
        return a - b;
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        int n = (int) in.nval;
        in.nextToken();
        int m = (int) in.nval;
        for (int i = 1; i <= n; i++) {
            in.nextToken();
            arr[i] = (int) in.nval;
        }
        build(n);
        for (int i = 1, l, r; i <= m; i++) {
            in.nextToken();
            l = (int) in.nval;
            in.nextToken();
            r = (int) in.nval;
            out.println(query(l, r));
        }
        out.flush();
        out.close();
        br.close();
    }

}
```
