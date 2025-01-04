# 带权并查集

![image](./images/一维数轴相对距离模型.png)

## [P8779 [蓝桥杯 2022 省 A] 推导部分和](https://www.luogu.com.cn/problem/P8779)

> - **Question 1**
>   - 有 `n` 个数字，下标 `1 ~ n` ，但是并不知道每个数字是多少，先给出 `m` 个数字段的累加和，再查询 `q` 个数字段的累加和，给出数字段累加和的操作 `l r v` ，代表 `l ~ r` 范围上的数字，累加和为 `v` ，查询数字段累加和的操作 `l r` ，代表查询 `l ~ r` 范围上的数字累加和，请根据 `m` 个给定，完成 `q` 个查询，如果某个查询无法给出答案，打印 `UNKNOWN` 。
>   - **Tips**
>     - `1 <= n, m, q <= 10^5`
>     - 累加和不会超过 `long` 类型范围

## [P2294 [HNOI2005] 狡猾的商人](https://www.luogu.com.cn/problem/P2294)

> - **Question 2**
>   - 有 `n` 个月的收入，下标 `1 ~ n` ，但是并不知道每个月收入是多少，操作 `l r v` ，代表从第l个月到第r个月，总收入为 `v` ，一共给你 `m` 个操作，请判断给定的数据是自洽还是自相矛盾，自洽打印 `true` ，自相矛盾打印 `false` 。
>   - **Tips**
>     - `1 <= n <= 100`
>     - `1 <= m <= 1000`
>     - 总收入不会超过 `int` 类型范围

## [How Many Answers Are Wrong](https://acm.hdu.edu.cn/showproblem.php?pid=3038)

> - **Question 3**
>   - 有 `n` 个数字，下标 `1 ~ n` ，但是并不知道每个数字是多少，操作 `l r v` ，代表 `l ~ r` 范围上累加和为 `v` ，一共 `m` 个操作，如果某个操作和之前的操作信息自相矛盾，认为当前操作是错误的，不进行这个操作，最后打印错误操作的数量。
>   - **Tips**
>     - `1 <= n <= 200000`
>     - `1 <= m <= 40000`
>     - 累加和不会超过 `int` 类型范围

## Java

> - **Question 1**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 100002;

    public static long INF = Long.MAX_VALUE;

    public static int n, m, q;

    public static int[] father = new int[MAXN];

    public static long[] dist = new long[MAXN];

    public static void prepare() {
        for (int i = 0; i <= n; i++) {
            father[i] = i;
            dist[i] = 0;
        }
    }

    public static int find(int i) {
        if (i != father[i]) {
            int tmp = father[i];
            father[i] = find(tmp);
            dist[i] += dist[tmp];
        }
        return father[i];
    }

    public static void union(int l, int r, long v) {
        int lf = find(l), rf = find(r);
        if (lf != rf) {
            father[lf] = rf;
            dist[lf] = v + dist[r] - dist[l];
        }
    }

    public static long query(int l, int r) {
        if (find(l) != find(r)) {
            return INF;
        }
        return dist[l] - dist[r];
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        n = (int) in.nval + 1;
        in.nextToken();
        m = (int) in.nval;
        in.nextToken();
        q = (int) in.nval;
        prepare();
        int l, r;
        long v;
        for (int i = 1; i <= m; i++) {
            in.nextToken();
            l = (int) in.nval;
            in.nextToken();
            r = (int) in.nval + 1;
            in.nextToken();
            v = (long) in.nval;
            union(l, r, v);
        }
        for (int i = 1; i <= q; i++) {
            in.nextToken();
            l = (int) in.nval;
            in.nextToken();
            r = (int) in.nval + 1;
            v = query(l, r);
            if (v == INF) {
                out.println("UNKNOWN");
            } else {
                out.println(v);
            }
        }
        out.flush();
        out.close();
        br.close();
    }

}
```

> - **Question 2**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 102;

    public static int t, n, m;

    public static boolean ans;

    public static int[] father = new int[MAXN];

    public static int[] dist = new int[MAXN];

    public static void prepare() {
        ans = true;
        for (int i = 1; i <= n; i++) {
            father[i] = i;
            dist[i] = 0;
        }
    }

    public static int find(int i) {
        if (i != father[i]) {
            int tmp = father[i];
            father[i] = find(tmp);
            dist[i] += dist[tmp];
        }
        return father[i];
    }

    public static void union(int l, int r, int v) {
        int lf = find(l), rf = find(r);
        if (lf != rf) {
            father[lf] = rf;
            dist[lf] = v + dist[r] - dist[l];
        }
    }

    public static boolean check(int l, int r, int v) {
        if (find(l) == find(r)) {
            if ((dist[l] - dist[r]) != v) {
                return false;
            }
        }
        return true;
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        t = (int) in.nval;
        for (int c = 1; c <= t; c++) {
            in.nextToken();
            n = (int) in.nval + 1;
            in.nextToken();
            m = (int) in.nval;
            prepare();
            for (int i = 1, l, r, v; i <= m; i++) {
                in.nextToken();
                l = (int) in.nval;
                in.nextToken();
                r = (int) in.nval + 1;
                in.nextToken();
                v = (int) in.nval;
                if (!check(l, r, v)) {
                    ans = false;
                } else {
                    union(l, r, v);
                }
            }
            out.println(ans);
        }
        out.flush();
        out.close();
        br.close();
    }

}
```

> - **Question 3**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 200002;

    public static int n, m, ans;

    public static int[] father = new int[MAXN];

    public static int[] dist = new int[MAXN];

    public static void prepare() {
        ans = 0;
        for (int i = 0; i <= n; i++) {
            father[i] = i;
            dist[i] = 0;
        }
    }

    public static int find(int i) {
        if (i != father[i]) {
            int tmp = father[i];
            father[i] = find(tmp);
            dist[i] += dist[tmp];
        }
        return father[i];
    }

    public static void union(int l, int r, int v) {
        int lf = find(l), rf = find(r);
        if (lf != rf) {
            father[lf] = rf;
            dist[lf] = v + dist[r] - dist[l];
        }
    }

    public static boolean check(int l, int r, int v) {
        if (find(l) == find(r)) {
            if ((dist[l] - dist[r]) != v) {
                return false;
            }
        }
        return true;
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            n = (int) in.nval + 1;
            in.nextToken();
            m = (int) in.nval;
            prepare();
            for (int i = 1, l, r, v; i <= m; i++) {
                in.nextToken();
                l = (int) in.nval;
                in.nextToken();
                r = (int) in.nval + 1;
                in.nextToken();
                v = (int) in.nval;
                if (!check(l, r, v)) {
                    ans++;
                } else {
                    union(l, r, v);
                }
            }
            out.println(ans);
        }
        out.flush();
        out.close();
        br.close();
    }

}
```
