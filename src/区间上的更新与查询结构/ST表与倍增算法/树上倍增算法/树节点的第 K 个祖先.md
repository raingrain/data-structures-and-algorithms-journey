# 树节点的第 K 个祖先

## [1483. 树节点的第 K 个祖先](https://leetcode.cn/problems/kth-ancestor-of-a-tree-node/)

> - ***Question***
>   - 给你一棵树，树上有 `n` 个节点，按从 `0` 到 `n - 1` 编号。树以父节点数组的形式给出，其中 `parent[i]` 是节点 `i` 的父节点。树的根节点是编号为 `0` 的节点。
>   - 树节点的第 `k` 个祖先节点是从该节点到根节点路径上的第 `k` 个节点。
>   - 实现 `TreeAncestor` 类：
>     - `TreeAncestor（int n， int[] parent）` 对树和父数组中的节点数初始化对象。
>     - `getKthAncestor(int node, int k)` 返回节点 `node` 的第 `k` 个祖先节点。如果不存在这样的祖先节点，返回 `-1` 。
>   - ***tips:***
>     - `1 <= k <= n <= 5 * 10^4`
>     - `parent[0] == -1` 表示编号为 `0` 的节点是根节点。
>     - 对于所有的 `0 < i < n, 0 <= parent[i] < n` 总成立
>     - `0 <= node < n`
>     - 至多查询 `5 * 10^4` 次

## Java

> - ***树上倍增***

```java
import java.util.Arrays;

class TreeAncestor {

    public static int MAXN = 50001;

    public static int LIMIT = 16;

    // 根据节点个数n，计算出2的几次方就够用了
    public static int power;

    public static int log2(int n) {
        int ans = 0;
        while ((1 << ans) <= (n >> 1)) {
            ans++;
        }
        return ans;
    }

    // 链式前向星建图
    public static int[] head = new int[MAXN];

    public static int[] next = new int[MAXN];

    public static int[] to = new int[MAXN];

    public static int cnt;

    // deep[i] : 节点i在第几层
    public static int[] deep = new int[MAXN];

    // stjump[i][p] : 节点i往上跳2的p次方步，到达的节点编号
    public static int[][] stjump = new int[MAXN][LIMIT];

    public TreeAncestor(int n, int[] parent) {
        power = log2(n);
        cnt = 1;
        Arrays.fill(head, 0, n, 0);
        for (int i = 1; i < parent.length; i++) {
            addEdge(parent[i], i);
        }
        dfs(0, 0);
    }

    public static void addEdge(int u, int v) {
        next[cnt] = head[u];
        to[cnt] = v;
        head[u] = cnt++;
    }

    // 当前来到i节点，i节点父亲节点是f
    public static void dfs(int i, int f) {
        if (i == 0) {
            deep[i] = 1;
        } else {
            deep[i] = deep[f] + 1;
        }
        stjump[i][0] = f;
        for (int p = 1; p <= power; p++) {
            stjump[i][p] = stjump[stjump[i][p - 1]][p - 1];
        }
        for (int e = head[i]; e != 0; e = next[e]) {
            dfs(to[e], i);
        }
    }

    public int getKthAncestor(int i, int k) {
        if (deep[i] <= k) {
            return -1;
        }
        // s是想要去往的层数
        int s = deep[i] - k;
        for (int p = power; p >= 0; p--) {
            if (deep[stjump[i][p]] >= s) {
                i = stjump[i][p];
            }
        }
        return i;
    }

}
```
