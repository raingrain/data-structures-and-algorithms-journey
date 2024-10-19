# 树的重心

1. 以某个节点为根时，最大子树的节点数最少，那么这个节点是重心。
2. 以某个节点为根时，每颗子树的节点数不超过总节点数的一半，那么这个节点是重心。
3. 以某个节点为根时，所有节点都走向该节点的总边数最少，那么这个节点是重心。
4. 一棵树最多有两个重心，如果有两个重心，那么两个重心一定相邻。
5. 如果树上增加或者删除一个叶节点，转移后的重心最多移动一条边。
6. 如果把两棵树连起来，那么新树的重心一定在原来两棵树重心的路径上。
7. 树上的边权如果都为正数，不管边权怎么分布，所有节点都走向重心的总距离和最小。

## [Balancing Act](http://poj.org/problem?id=1655)

> - ***Question 1***
>   - 一共有 `n` 个节点，编号 `1 ~ n` ，有 `n - 1` 条边形成一棵树，返回重心点和重心点最大子树的节点数。

## [Godfather](http://poj.org/problem?id=3107)

> - ***Question 2***
>   - 一共有 `n` 个节点，编号 `1 ~ n` ，有 `n - 1` 条边形成一棵树，返回所有重心点。

---

## *Java*

> - ***Question 1: 性质1***

```java
import java.io.*;
import java.util.Arrays;

public class Main {

    public static int MAXN = 20001;

    public static int n;

    public static int[] head = new int[MAXN];

    public static int[] next = new int[MAXN << 1];

    public static int[] to = new int[MAXN << 1];

    public static int cnt;

    public static int[] size = new int[MAXN];

    public static int center;

    public static int best;

    public static void build() {
        cnt = 1;
        Arrays.fill(head, 1, n + 1, 0);
        best = Integer.MAX_VALUE;
    }

    public static void addEdge(int u, int v) {
        next[cnt] = head[u];
        to[cnt] = v;
        head[u] = cnt++;
    }

    // 当前来到u节点，父亲节点是f
    public static void dfs(int u, int f) {
        size[u] = 1;
        // 以当前节点u做根节点，最大的子树有多少节点
        int maxsub = 0;
        for (int e = head[u], v; e != 0; e = next[e]) {
            v = to[e];
            if (v != f) {
                dfs(v, u);
                size[u] += size[v];
                maxsub = Math.max(maxsub, size[v]);
            }
        }
        maxsub = Math.max(maxsub, n - size[u]);
        // 题目要求找到编号最小的重心
        if (maxsub < best || (maxsub == best && u < center)) {
            best = maxsub;
            center = u;
        }
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        int testCase = (int) in.nval;
        for (int t = 1; t <= testCase; t++) {
            in.nextToken();
            n = (int) in.nval;
            build();
            for (int i = 1, u, v; i < n; i++) {
                in.nextToken();
                u = (int) in.nval;
                in.nextToken();
                v = (int) in.nval;
                addEdge(u, v);
                addEdge(v, u);
            }
            dfs(1, 0);
            out.println(center + " " + best);
        }
        out.flush();
        out.close();
        br.close();
    }

}
```

> - ***Question 2: 性质2***

```java
import java.io.*;
import java.util.Arrays;

public class Main {

    public static int MAXN = 50001;

    public static int n;

    public static int[] head = new int[MAXN];

    public static int[] next = new int[MAXN << 1];

    public static int[] to = new int[MAXN << 1];

    public static int cnt;

    public static int[] size = new int[MAXN];

    public static int[] maxsub = new int[MAXN];

    public static void build() {
        cnt = 1;
        Arrays.fill(head, 1, n + 1, 0);
    }

    public static void addEdge(int u, int v) {
        next[cnt] = head[u];
        to[cnt] = v;
        head[u] = cnt++;
    }

    public static void dfs(int u, int f) {
        size[u] = 1;
        maxsub[u] = 0;
        for (int e = head[u], v; e != 0; e = next[e]) {
            v = to[e];
            if (v != f) {
                dfs(v, u);
                size[u] += size[v];
                maxsub[u] = Math.max(maxsub[u], size[v]);
            }
        }
        maxsub[u] = Math.max(maxsub[u], n - size[u]);
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        n = (int) in.nval;
        build();
        for (int i = 1, u, v; i < n; i++) {
            in.nextToken();
            u = (int) in.nval;
            in.nextToken();
            v = (int) in.nval;
            addEdge(u, v);
            addEdge(v, u);
        }
        dfs(1, 0);
        int m = 0;
        int[] centers = new int[2];
        for (int i = 1; i <= n; i++) {
            if (maxsub[i] <= n / 2) {
                centers[m++] = i;
            }
        }
        if (m == 1) {
            out.println(centers[0]);
        } else { // m == 2
            out.println(centers[0] + " " + centers[1]);
        }
        out.flush();
        out.close();
        br.close();
    }

}
```

---

> ***last change: 2024/5/19***

---
