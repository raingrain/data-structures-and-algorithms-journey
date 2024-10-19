# 树的直径

树上距离最远的两个点，形成的路径叫做树的直径

树的直径两种求法：

1. 两次DFS的方式，适用于边权非负的树。优点：不仅能得到直径的长度，还能得到直径沿途所有点，等等很多信息。缺点：遍历两遍树。
2. 树型DP的方式，适用于所有树。优点：遍历一遍树。缺点：只能得到直径的长度。

如果树上的边权都为正，则有如下直径相关的结论：

1. 如果有多条直径，那么这些直径一定拥有共同的中间部分，可能是一个公共点或一段公共路径。
2. 树上任意一点，相隔最远的点的集合，直径的两端点至少有一个在其中。

## [U81904 【模板】树的直径](https://www.luogu.com.cn/problem/U81904)

> - ***Question***
>   - 给定一棵树，树中每条边都有一个权值，树中两点之间的距离定义为连接两点的路径边权之和。树中最远的两个节点之间的距离被称为树的直径，连接这两点的路径被称为树的最长链。现在让你求出树的最长链的距离
>   - ***输入描述***
>     - 给定一棵无根树。
>     - 第一行为一个正整数 `n` 表示这颗树有 `n` 个节点。
>     - 接下来的 `n - 1` 行,每行三个正整数 `u, v, w` ，表示 `u, v (u, v <= n)` 有一条权值为 `w` 的边相连。
>     - 数据保证没有重边或自环。
>   - ***输出描述***
>     - 输入仅一行，表示树的最长链的距离。
>   - ***tips:***
>     - `n <= 500000`

---

## *Java*

> - ***DFS***

```java
import java.io.*;
import java.util.Arrays;

public class Main {

    public static int MAXN = 500001;

    public static int n;

    public static int[] head = new int[MAXN];

    public static int[] next = new int[MAXN << 1];

    public static int[] to = new int[MAXN << 1];

    public static int[] weight = new int[MAXN << 1];

    public static int cnt;

    // 直径的开始点
    public static int start;

    // 直径的结束点
    public static int end;

    // 直径长度
    public static int diameter;

    // dist[i] : 从规定的头节点出发，走到i的距离
    public static int[] dist = new int[MAXN];

    // last[i] : 从规定的头节点出发，i节点的上一个节点
    public static int[] last = new int[MAXN];

    public static void build() {
        cnt = 1;
        Arrays.fill(head, 1, n + 1, 0);
    }

    public static void addEdge(int u, int v, int w) {
        next[cnt] = head[u];
        to[cnt] = v;
        weight[cnt] = w;
        head[u] = cnt++;
    }

    public static void road() {
        dfs(1, 0, 0);
        start = 1;
        for (int i = 2; i <= n; i++) {
            if (dist[i] > dist[start]) {
                start = i;
            }
        }
        dfs(start, 0, 0);
        end = 1;
        for (int i = 2; i <= n; i++) {
            if (dist[i] > dist[end]) {
                end = i;
            }
        }
        diameter = dist[end];
    }

    public static void dfs(int u, int f, int w) {
        last[u] = f;
        dist[u] = dist[f] + w;
        for (int e = head[u]; e != 0; e = next[e]) {
            if (to[e] != f) {
                dfs(to[e], u, weight[e]);
            }
        }
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        n = (int) in.nval;
        build();
        for (int i = 1, u, v, w; i < n; i++) {
            in.nextToken();
            u = (int) in.nval;
            in.nextToken();
            v = (int) in.nval;
            in.nextToken();
            w = (int) in.nval;
            addEdge(u, v, w);
            addEdge(v, u, w);
        }
        road();
        out.println(diameter);
        out.flush();
        out.close();
        br.close();
    }

}
```

> - ***树型DP***

```java
import java.io.*;
import java.util.Arrays;

public class Main {

    public static int MAXN = 500001;

    public static int n;

    public static int[] head = new int[MAXN];

    public static int[] next = new int[MAXN << 1];

    public static int[] to = new int[MAXN << 1];

    public static int[] weight = new int[MAXN << 1];

    public static int cnt;

    // dist[u] : 从u开始必须往下走，能走出的最大距离，可以不选任何边
    public static int[] dist = new int[MAXN];

    // ans[u] : 路径必须包含点u的情况下，最大路径和
    public static int[] ans = new int[MAXN];

    public static void build() {
        cnt = 1;
        Arrays.fill(head, 1, n + 1, 0);
        Arrays.fill(dist, 1, n + 1, 0);
        Arrays.fill(ans, 1, n + 1, 0);
    }

    public static void addEdge(int u, int v, int w) {
        next[cnt] = head[u];
        to[cnt] = v;
        weight[cnt] = w;
        head[u] = cnt++;
    }

    public static void dp(int u, int f) {
        for (int e = head[u], v; e != 0; e = next[e]) {
            v = to[e];
            if (v != f) {
                dp(v, u);
            }
        }
        for (int e = head[u], v; e != 0; e = next[e]) {
            v = to[e];
            if (v != f) {
                ans[u] = Math.max(ans[u], dist[u] + dist[v] + weight[e]);
                dist[u] = Math.max(dist[u], dist[v] + weight[e]);
            }
        }
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        n = (int) in.nval;
        build();
        for (int i = 1, u, v, w; i < n; i++) {
            in.nextToken();
            u = (int) in.nval;
            in.nextToken();
            v = (int) in.nval;
            in.nextToken();
            w = (int) in.nval;
            addEdge(u, v, w);
            addEdge(v, u, w);
        }
        dp(1, 0);
        int diameter = Integer.MIN_VALUE;
        for (int i = 1; i <= n; i++) {
            diameter = Math.max(diameter, ans[i]);
        }
        out.println(diameter);
        out.flush();
        out.close();
        br.close();
    }

}
```

> - ***树型DP简化版***

```java
import java.io.*;
import java.util.Arrays;

public class Main {

    public static int MAXN = 500001;

    public static int n;

    public static int[] head = new int[MAXN];

    public static int[] next = new int[MAXN << 1];

    public static int[] to = new int[MAXN << 1];

    public static int[] weight = new int[MAXN << 1];

    public static int cnt;

    public static int[] dist = new int[MAXN];

    // 直径的长度
    public static int diameter;

    public static void build() {
        cnt = 1;
        Arrays.fill(head, 1, n + 1, 0);
        Arrays.fill(dist, 1, n + 1, 0);
        diameter = Integer.MIN_VALUE;
    }

    public static void addEdge(int u, int v, int w) {
        next[cnt] = head[u];
        to[cnt] = v;
        weight[cnt] = w;
        head[u] = cnt++;
    }

    public static void dp(int u, int f) {
        for (int e = head[u], v; e != 0; e = next[e]) {
            v = to[e];
            if (v != f) {
                dp(v, u);
            }
        }
        for (int e = head[u], v; e != 0; e = next[e]) {
            v = to[e];
            if (v != f) {
                // 干脆直接去更新diameter
                diameter = Math.max(diameter, dist[u] + dist[v] + weight[e]);
                dist[u] = Math.max(dist[u], dist[v] + weight[e]);
            }
        }
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        n = (int) in.nval;
        build();
        for (int i = 1, u, v, w; i < n; i++) {
            in.nextToken();
            u = (int) in.nval;
            in.nextToken();
            v = (int) in.nval;
            in.nextToken();
            w = (int) in.nval;
            addEdge(u, v, w);
            addEdge(v, u, w);
        }
        dp(1, 0);
        out.println(diameter);
        out.flush();
        out.close();
        br.close();
    }

}
```
