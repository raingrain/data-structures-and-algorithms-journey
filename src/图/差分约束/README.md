# 差分约束

## 差分约束的两种形式 & 差分约束转化成利用SPFA算法判断负环或无限增长环

![image](./images/差分约束的两种形式.png)

## 若干变量确定的差分约束建图 & 差分约束和Floyd算法结合

![image](./images/若干变量确定的差分约束建图.png)

## [P5960 【模板】差分约束](https://www.luogu.com.cn/problem/P5960)

> - ***Question 1***
>   - 一共有 `n` 个变量，编号 `1 ~ n` ，给定 `m` 个不等式，每个不等式的形式为 `Xi - Xj <= Ci` ，其中 `Xi` 和 `Xj` 为变量， `Ci` 为常量，如果不等式存在矛盾导致无解，打印 `NO` ，如果有解，打印满足所有不等式的其中一组解 `X1, X2, ...` 。
>   - ***tips:***
>     - `1 <= n、m <= 5 * 10^3`
>     - `-10^4 <= Ci <= +10^4`

## [P1993 小 K 的农场](https://www.luogu.com.cn/problem/P1993)

> - ***Question 2***
>   - 一共有 `n` 个农场，编号 `1 ~ n` ，给定 `m` 条关系，每条关系是如下三种形式中的一种
>     - 关系 `a b c` 表示农场 `a` 比农场 `b` 至少多种植了 `c` 个作物。
>     - 关系 `a b c` 表示农场 `a` 比农场 `b` 至多多种植了 `c` 个作物。
>     - 关系 `a b` 表示农场 `a` 和农场 `b` 种植了一样多的作物。
>   - 如果关系之间能推出矛盾，打印 `No` ，不存在矛盾，打印 `Yes` 。
>   - ***tips:***
>     - `1 <= n、m <= 5 * 10^3`
>     - `1 <= c <= 5 * 10^3`

## Java

> - ***Question 1: 形式1***

```java
import java.io.*;
import java.util.Arrays;

public class Main {

    public static int MAXN = 5001;

    public static int MAXM = 10001;

    // 链式前向星需要
    public static int[] head = new int[MAXN];

    public static int[] next = new int[MAXM];

    public static int[] to = new int[MAXM];

    public static int[] weight = new int[MAXM];

    public static int cnt;

    // spfa需要
    public static int[] dist = new int[MAXN];

    public static int[] update = new int[MAXN];

    public static int MAXQ = 5000001;

    public static int[] queue = new int[MAXQ];

    public static int h, t;

    public static boolean[] enter = new boolean[MAXN];

    public static int n, m;

    public static void prepare() {
        cnt = 1;
        h = t = 0;
        Arrays.fill(head, 0, n + 1, 0);
        // 所有距离先设置成最大值
        Arrays.fill(dist, 0, n + 1, Integer.MAX_VALUE);
        Arrays.fill(update, 0, n + 1, 0);
        Arrays.fill(enter, 0, n + 1, false);
    }

    public static void addEdge(int u, int v, int w) {
        next[cnt] = head[u];
        to[cnt] = v;
        weight[cnt] = w;
        head[u] = cnt++;
    }

    // 从一个可以到达所有节点的源点出发，如果任意节点进入队列次数大于节点总数-1，说明有负环
    public static boolean spfa(int s) {
        dist[s] = 0;
        update[s] = 1;
        queue[t++] = s;
        enter[s] = true;
        while (h < t) {
            int u = queue[h++];
            enter[u] = false;
            for (int ei = head[u], v, w; ei > 0; ei = next[ei]) {
                v = to[ei];
                w = weight[ei];
                if (dist[v] > dist[u] + w) { // 变小才更新
                    dist[v] = dist[u] + w;
                    if (!enter[v]) {
                        // 因为节点0是额外增加的超级源点
                        // 所以节点数量增加了1个，所以这么判断
                        if (++update[v] > n) {
                            return true;
                        }
                        queue[t++] = v;
                        enter[v] = true;
                    }
                }
            }
        }
        return false;
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        n = (int) in.nval;
        in.nextToken();
        m = (int) in.nval;
        prepare();
        // 0号点是连通超级源点，保证图的连通性
        for (int i = 1; i <= n; i++) {
            addEdge(0, i, 0);
        }
        for (int i = 1, u, v, w; i <= m; i++) {
            in.nextToken();
            u = (int) in.nval;
            in.nextToken();
            v = (int) in.nval;
            in.nextToken();
            w = (int) in.nval;
            // 形式1的连边方式
            addEdge(v, u, w);
        }
        if (spfa(0)) {
            out.println("NO");
        } else {
            for (int i = 1; i <= n; i++) {
                out.print(dist[i] + " ");
            }
            out.println();
        }
        out.flush();
        out.close();
        br.close();
    }

}
```

> - ***Question 1: 形式2***

```java
import java.io.*;
import java.util.Arrays;

public class Main {

    public static int MAXN = 5001;

    public static int MAXM = 10001;

    // 链式前向星需要
    public static int[] head = new int[MAXN];

    public static int[] next = new int[MAXM];

    public static int[] to = new int[MAXM];

    public static int[] weight = new int[MAXM];

    public static int cnt;

    // spfa需要
    public static int[] dist = new int[MAXN];

    public static int[] update = new int[MAXN];

    public static int MAXQ = 5000001;

    public static int[] queue = new int[MAXQ];

    public static int h, t;

    public static boolean[] enter = new boolean[MAXN];

    public static int n, m;

    public static void prepare() {
        cnt = 1;
        h = t = 0;
        Arrays.fill(head, 0, n + 1, 0);
        // 所有距离先设置成最小值
        Arrays.fill(dist, 0, n + 1, Integer.MIN_VALUE);
        Arrays.fill(update, 0, n + 1, 0);
        Arrays.fill(enter, 0, n + 1, false);
    }

    public static void addEdge(int u, int v, int w) {
        next[cnt] = head[u];
        to[cnt] = v;
        weight[cnt] = w;
        head[u] = cnt++;
    }

    // spfa判断无限增加环，s是超级源点
    public static boolean spfa(int s) {
        dist[s] = 0;
        update[s] = 1;
        queue[t++] = s;
        enter[s] = true;
        while (h < t) {
            int u = queue[h++];
            enter[u] = false;
            for (int ei = head[u], v, w; ei > 0; ei = next[ei]) {
                v = to[ei];
                w = weight[ei];
                if (dist[v] < dist[u] + w) { // 变大才更新
                    dist[v] = dist[u] + w;
                    if (!enter[v]) {
                        // 因为节点0是额外增加的超级源点
                        // 所以节点数量增加了1个，所以这么判断
                        if (++update[v] > n) {
                            return true;
                        }
                        queue[t++] = v;
                        enter[v] = true;
                    }
                }
            }
        }
        return false;
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        n = (int) in.nval;
        in.nextToken();
        m = (int) in.nval;
        prepare();
        // 0号点是连通超级源点，保证图的连通性
        for (int i = 1; i <= n; i++) {
            addEdge(0, i, 0);
        }
        for (int i = 1, u, v, w; i <= m; i++) {
            in.nextToken();
            u = (int) in.nval;
            in.nextToken();
            v = (int) in.nval;
            in.nextToken();
            w = (int) in.nval;
            // 形式2的连边方式
            addEdge(u, v, -w);
        }
        if (spfa(0)) {
            out.println("NO");
        } else {
            for (int i = 1; i <= n; i++) {
                out.print(dist[i] + " ");
            }
            out.println();
        }
        out.flush();
        out.close();
        br.close();
    }

}
```

> - ***Question 2: 形式1***

```java
import java.io.*;
import java.util.Arrays;

public class Main {

    public static int MAXN = 5001;

    public static int MAXM = 20001;

    // 链式前向星需要
    public static int[] head = new int[MAXN];

    public static int[] next = new int[MAXM];

    public static int[] to = new int[MAXM];

    public static int[] weight = new int[MAXM];

    public static int cnt;

    // spfa需要
    public static int[] dist = new int[MAXN];

    public static int[] update = new int[MAXN];

    public static int MAXQ = 20000001;

    public static int[] queue = new int[MAXQ];

    public static int h, t;

    public static boolean[] enter = new boolean[MAXN];

    public static int n, m;

    public static void prepare() {
        cnt = 1;
        h = t = 0;
        Arrays.fill(head, 0, n + 1, 0);
        Arrays.fill(dist, 0, n + 1, Integer.MAX_VALUE);
        Arrays.fill(update, 0, n + 1, 0);
        Arrays.fill(enter, 0, n + 1, false);
    }

    public static void addEdge(int u, int v, int w) {
        next[cnt] = head[u];
        to[cnt] = v;
        weight[cnt] = w;
        head[u] = cnt++;
    }

    public static boolean spfa(int s) {
        dist[s] = 0;
        update[s] = 1;
        queue[t++] = s;
        enter[s] = true;
        while (h < t) {
            int u = queue[h++];
            enter[u] = false;
            for (int ei = head[u], v, w; ei > 0; ei = next[ei]) {
                v = to[ei];
                w = weight[ei];
                if (dist[v] > dist[u] + w) {
                    dist[v] = dist[u] + w;
                    if (!enter[v]) {
                        if (++update[v] > n) {
                            return true;
                        }
                        queue[t++] = v;
                        enter[v] = true;
                    }
                }
            }
        }
        return false;
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        n = (int) in.nval;
        in.nextToken();
        m = (int) in.nval;
        prepare();
        for (int i = 1; i <= n; i++) {
            addEdge(0, i, 0);
        }
        for (int i = 1, type, u, v, w; i <= m; i++) {
            in.nextToken();
            type = (int) in.nval;
            in.nextToken();
            u = (int) in.nval;
            in.nextToken();
            v = (int) in.nval;
            if (type == 1) {
                in.nextToken();
                w = (int) in.nval;
                addEdge(u, v, -w);
            } else if (type == 2) {
                in.nextToken();
                w = (int) in.nval;
                addEdge(v, u, w);
            } else {
                addEdge(u, v, 0);
                addEdge(v, u, 0);
            }
        }
        if (spfa(0)) {
            out.println("No");
        } else {
            out.println("Yes");
        }
        out.flush();
        out.close();
        br.close();
    }

}
```
