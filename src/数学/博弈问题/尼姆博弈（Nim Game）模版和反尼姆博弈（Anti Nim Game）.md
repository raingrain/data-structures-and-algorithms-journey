# 尼姆博弈（Nim Game）模版和反尼姆博弈（Anti Nim Game）

## [P2197 【模板】Nim 游戏](https://www.luogu.com.cn/problem/P2197)

> - ***Question 1***
>   - 甲，乙两个人玩 `nim` 取石子游戏。`nim` 游戏的规则是这样的：地上有 `n` 堆石子（每堆石子数量小于 `10^4` ），每人每次可从任意一堆石子里取出任意多枚石子扔掉，可以取完，不能不取。每次只能从一堆里取。最后没石子可取的人就输了。假如甲是先手，且告诉你这 `n` 堆石子的数量，他想知道是否存在先手必胜的策略。
>   - ***输入描述***
>     - 第一行一个整数 `T (T <= 10)` ，表示有 `T` 组数据。
>     - 接下来每两行是一组数据，第一行一个整数 `n (n <= 10^4)` ，表示有 `n` 堆石子。第二行有 `n` 个数，表示每一堆石子的数量。
>   - ***输出描述***
>     - 共 `T` 行，每行表示如果对于这组数据存在先手必胜策略则输出 `Yes` ，否则输出 `No` 。

## [P4279 [SHOI2008] 小约翰的游戏](https://www.luogu.com.cn/problem/P4279)

> - ***Question 2***
>   - 小约翰经常和他的哥哥玩一个非常有趣的游戏：桌子上有 `n` 堆石子，小约翰和他的哥哥轮流取石子，每个人取的时候，可以随意选择一堆石子，在这堆石子中取走任意多的石子，但不能一粒石子也不取，我们规定取到最后一粒石子的人算输。
>   - 小约翰相当固执，他坚持认为先取的人有很大的优势，所以他总是先取石子，而他的哥哥就聪明多了，他从来没有在游戏中犯过错误。小约翰一怒之前请你来做他的参谋。自然，你应该先写一个程序，预测一下谁将获得游戏的胜利。
>   - ***输入描述***
>     - 本题的输入由多组数据组成，第一行包括一个整数 `T (1 <= T <= 500)` ，表示输入总共有 `T` 组数据。
>     - 每组数据的第一行包括一个整数 `N (1 <= N <= 50)` ，表示共有 `N` 堆石子，接下来有 `N` 个不超过 `5000` 的整数，分别表示每堆石子的数目。
>   - ***输出描述***
>     - 对于每组数据，如果约翰能赢得比赛，则输出 `John` ，否则输出 `Brother` ，请注意单词的大小写。

---

## *Java*

> - ***Question 1: 尼姆博弈模版***
>   - 所有堆数异或结果不为 `0` 时先手必胜，为 `0` 时后手必胜。

```java
import java.io.*;

public class Main {

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        int t = (int) in.nval;
        for (int i = 0; i < t; i++) {
            in.nextToken();
            int n = (int) in.nval;
            int eor = 0;
            for (int j = 0; j < n; j++) {
                in.nextToken();
                eor ^= (int) in.nval;
            }
            if (eor != 0) {
                out.println("Yes");
            } else {
                out.println("No");
            }
        }
        out.flush();
        out.close();
        br.close();
    }

}
```

> - ***Question 2: 反尼姆博弈***

```java
import java.io.*;

public class Main {

    public static int MAXN = 51;

    public static int[] stones = new int[MAXN];

    public static int t, n;

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        t = (int) in.nval;
        for (int i = 0; i < t; i++) {
            in.nextToken();
            n = (int) in.nval;
            for (int j = 0; j < n; j++) {
                in.nextToken();
                stones[j] = (int) in.nval;
            }
            out.println(compute());
        }
        out.flush();
        out.close();
        br.close();
    }

    public static String compute() {
        int eor = 0, sum = 0;
        for (int i = 0; i < n; i++) {
            eor ^= stones[i];
            sum += stones[i] == 1 ? 1 : 0;
        }
        if (sum == n) {
            return (n & 1) == 1 ? "Brother" : "John";
        } else {
            return eor != 0 ? "John" : "Brother";
        }
    }

}
```
