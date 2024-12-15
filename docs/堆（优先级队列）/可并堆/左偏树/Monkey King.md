# Monkey King

## [P1456 Monkey King](https://www.luogu.com.cn/problem/P1456)

> - **Question**
>   - Once in a forest, there lived N aggressive monkeys. At the beginning, they each does things in its own way and none of them knows each other. But monkeys can't avoid quarrelling, and it only happens between two monkeys who does not know each other. And when it happens, both the two monkeys will invite the strongest friend of them, and duel. Of course, after the duel, the two monkeys and all of their friends will know each other, and the quarrel above will no longer happen among these monkeys even if they have ever conflicted.
>   - Assume that every money has a strongness value, which will be reduced to only half of the original after a duel(that is, 10 will be reduced to 5 and 5 will be reduced to 2).
>   - And we also assume that every monkey knows himself. That is, when he is the strongest one in all of his friends, he himself will go to duel.
>   - **输入描述**
>     - There are several test cases, and each case consists of two parts.
>     - First part: The first line contains an integer N(N<=100,000), which indicates the number of monkeys. And then N lines follows. There is one number on each line, indicating the strongness value of ith monkey(<=32768).
>     - Second part: The first line contains an integer M(M<=100,000), which indicates there are M conflicts happened. And then M lines follows, each line of which contains two integers x and y, indicating that there is a conflict between the Xth monkey and Yth.
>   - **输出描述**
>     - For each of the conflict, output -1 if the two monkeys know each other, otherwise output the strength value of the strongest monkey among all of its friends after the duel.
>   - **Tips**
>     - `N, M <= 100000`
>     - `si <= 32768`

## Java

> - **左偏树**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 100001;

    public static int n, m;

    public static int[] num = new int[MAXN];

    public static int[] left = new int[MAXN];

    public static int[] right = new int[MAXN];

    public static int[] dist = new int[MAXN];

    public static int[] father = new int[MAXN];

    public static void prepare() {
        dist[0] = -1;
        for (int i = 1; i <= n; i++) {
            left[i] = right[i] = dist[i] = 0;
            father[i] = i;
        }
    }

    public static int find(int i) {
        father[i] = father[i] == i ? i : find(father[i]);
        return father[i];
    }

    public static int merge(int i, int j) {
        if (i == 0 || j == 0) {
            return i + j;
        }
        int tmp;
        // 维护大根堆
        if (num[i] < num[j]) {
            tmp = i;
            i = j;
            j = tmp;
        }
        right[i] = merge(right[i], j);
        if (dist[left[i]] < dist[right[i]]) {
            tmp = left[i];
            left[i] = right[i];
            right[i] = tmp;
        }
        dist[i] = dist[right[i]] + 1;
        father[left[i]] = father[right[i]] = i;
        return i;
    }

    public static int pop(int i) {
        father[left[i]] = left[i];
        father[right[i]] = right[i];
        father[i] = merge(left[i], right[i]);
        left[i] = right[i] = dist[i] = 0;
        return father[i];
    }

    public static int fight(int x, int y) {
        int a = find(x);
        int b = find(y);
        if (a == b) {
            return -1;
        }
        int l = pop(a);
        int r = pop(b);
        num[a] /= 2;
        num[b] /= 2;
        father[a] = father[b] = father[l] = father[r] = merge(merge(l, a), merge(r, b));
        return num[father[a]];
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            n = (int) in.nval;
            prepare();
            for (int i = 1; i <= n; i++) {
                in.nextToken();
                num[i] = (int) in.nval;
            }
            in.nextToken();
            m = (int) in.nval;
            for (int i = 1, x, y; i <= m; i++) {
                in.nextToken();
                x = (int) in.nval;
                in.nextToken();
                y = (int) in.nval;
                out.println(fight(x, y));
            }
        }
        out.flush();
        out.close();
        br.close();
    }

}
```
