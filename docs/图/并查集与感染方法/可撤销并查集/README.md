# 可撤销并查集

1. 既然没有扁平化了，那么每次合并，也就是修改两个集合头节点的信息。
2. 使用一个栈，记录如何恢复之前的信息即可。
3. 撤销操作时，依次弹出栈里的信息，恢复过来即可。

## [AT_abc302_h [ABC302Ex] Ball Collector](https://www.luogu.com.cn/problem/AT_abc302_h)

> - **Question**
>   - 一共有 `n` 个点，每个点有两个小球，每个点给定两个小球的编号，一共有 `n - 1` 条无向边，所有节点连成一棵树，对 `i` 号点， `2 <= i <= n` ，都计算如下问题的答案并打印，从 `1` 号点到 `i` 号点的最短路径上，每个点只能拿一个小球，最多能拿几个编号不同的小球。
>   - **Tips**
>     - `1 <= n <= 2 * 10^5`

## Java

> - **Question: 连通区边的数量和拿球数量的分析 + 树上dfs + 可撤销并查集**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 200001;
    public static int[][] arr = new int[MAXN][2];

    public static int[] head = new int[MAXN];
    public static int[] next = new int[MAXN << 1];
    public static int[] to = new int[MAXN << 1];
    public static int cnt;

    public static int[] father = new int[MAXN];
    public static int[] siz = new int[MAXN];
    public static int[] edgeCnt = new int[MAXN];

    public static int[][] rollback = new int[MAXN][2];
    public static int opsize = 0;

    public static int[] ans = new int[MAXN];
    public static int ball = 0;

    public static void addEdge(int u, int v) {
        next[++cnt] = head[u];
        to[cnt] = v;
        head[u] = cnt;
    }

    public static int find(int i) {
        while (i != father[i]) {
            i = father[i];
        }
        return i;
    }

    public static void union(int x, int y) {
        int fx = find(x);
        int fy = find(y);
        if (siz[fx] < siz[fy]) {
            int tmp = fx;
            fx = fy;
            fy = tmp;
        }
        father[fy] = fx;
        siz[fx] += siz[fy];
        edgeCnt[fx] += edgeCnt[fy] + 1;
        rollback[++opsize][0] = fx;
        rollback[opsize][1] = fy;
    }

    public static void undo() {
        int fx = rollback[opsize][0];
        int fy = rollback[opsize--][1];
        father[fy] = fy;
        siz[fx] -= siz[fy];
        edgeCnt[fx] -= edgeCnt[fy] + 1;
    }

    public static void dfs(int u, int fa) {
        int fx = find(arr[u][0]);
        int fy = find(arr[u][1]);
        boolean added = false;
        boolean unioned = false;
        if (fx == fy) {
            if (edgeCnt[fx] < siz[fx]) {
                ball++;
                added = true;
            }
            edgeCnt[fx]++;
        } else {
            if (edgeCnt[fx] < siz[fx] || edgeCnt[fy] < siz[fy]) {
                ball++;
                added = true;
            }
            union(fx, fy);
            unioned = true;
        }
        ans[u] = ball;
        for (int e = head[u]; e > 0; e = next[e]) {
            if (to[e] != fa) {
                dfs(to[e], u);
            }
        }
        if (added) {
            ball--;
        }
        if (unioned) {
            undo();
        } else {
            edgeCnt[fx]--;
        }
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        int n = (int) in.nval;
        for (int i = 1; i <= n; i++) {
            in.nextToken();
            arr[i][0] = (int) in.nval;
            in.nextToken();
            arr[i][1] = (int) in.nval;
        }
        for (int i = 1, u, v; i < n; i++) {
            in.nextToken();
            u = (int) in.nval;
            in.nextToken();
            v = (int) in.nval;
            addEdge(u, v);
            addEdge(v, u);
        }
        for (int i = 1; i <= n; i++) {
            father[i] = i;
            siz[i] = 1;
            edgeCnt[i] = 0;
        }
        dfs(1, 0);
        for (int i = 2; i < n; i++) {
            out.print(ans[i] + " ");
        }
        out.println(ans[n]);
        out.flush();
        out.close();
        br.close();
    }

}
```
