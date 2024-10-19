# 普通二叉树或BST中任意两个节点的最近公共祖先

## [236. 二叉树的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree/)

## [面试题 04.08. 首个共同祖先](https://leetcode.cn/problems/first-common-ancestor-lcci/)

## [剑指 Offer 68 - II. 二叉树的最近公共祖先](https://leetcode.cn/problems/er-cha-shu-de-zui-jin-gong-gong-zu-xian-lcof/)

## [235. 二叉搜索树的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-search-tree/)

## [剑指 Offer 68 - I. 二叉搜索树的最近公共祖先](https://leetcode.cn/problems/er-cha-sou-suo-shu-de-zui-jin-gong-gong-zu-xian-lcof/)

> - ***Question 1***
>   - 给定一个二叉树的头节点 `root` , 找到该树中两个指定节点的最近公共祖先。
>   - 对于有根树 `T` 的两个节点 `p` 、 `q` ，最近公共祖先表示为一个节点 `x` ，满足 `x` 是 `p、q` 的祖先且 `x` 的深度尽可能大（一个节点也可以是它自己的祖先）。
> - ***Question 2***
>   - 给定一个二叉搜索树的头节点 `root` , 找到该树中两个指定节点的最近公共祖先。
> - ***tips:***
>   - 树中节点数目在范围 `[2, 10^5]` 内
>   - `-10^9 <= Node.val <= 10^9`
>   - 所有 `Node.val` 互不相同
>   - `p != q`
>   - `p` 和 `q` 均存在于给定的二叉树/BST中

## 程序员代码面试指南：IT名企算法与数据结构题目最优解（第二版） 第3章 二叉树问题 Tarjan算法与并查集解决二叉树节点间最近公共祖先的批量查询问题

> - ***Question 2***
>   - 实现离线批量查询两个节点的最近公共祖先。

## 左老师课堂讲述

> - ***Question 3***
>   - 实现在线批量查询两个节点的最近公共祖先。

## [P3379 【模板】最近公共祖先（LCA）](https://www.luogu.com.cn/problem/P3379)

> - ***Question 4***
>   - 如题，给定一棵有根多叉树，请求出指定两个点直接最近的公共祖先。
>   - ***输入描述***
>     - 第一行包含三个正整数 `N, M, S` ，分别表示树的结点个数、询问的个数和树根结点的序号。
>     - 接下来 `N - 1` 行每行包含两个正整数 `x, y` ，表示 `x` 结点和 `y` 结点之间有一条直接连接的边（数据保证可以构成树）。
>     - 接下来 `M` 行每行包含两个正整数 `a, b`，表示询问 `a` 结点和 `b` 结点的最近公共祖先。
>   - ***输出描述***
>     - 输出包含 `M` 行，每行包含一个正整数，依次为每一个询问的结果。
>   - ***tips:***
>     - `1 <= N, M <= 500000`
>     - `1 <= x, y, a, b <= N`

## Java

> - ***哈希表***
>   - 我们可以用哈希表存储所有节点的父节点，然后我们就可以利用节点的父节点信息从 `p` 结点开始不断往上跳，并记录已经访问过的节点，再从 `q` 节点开始不断往上跳，如果碰到已经访问过的节点，那么这个节点就是我们要找的最近公共祖先。
>   - 从根节点开始遍历整棵二叉树，用 `HashMap` 记录每个节点的父节点指针。
>   - 从 `p` 节点开始不断往它的祖先移动，并用 `HashSet` 记录已经访问过的祖先节点。
>   - 同样，我们再从 `q` 节点开始不断往它的祖先移动，如果有祖先已经被访问过，即意味着这是 `p` 和 `q` 的深度最深的公共祖先，即 `LCA` 节点。
>   - 这种思想本质上是找到从根节点到 `q` 和 `p` 节点的两条路径叠合在一起时的分叉点。
> - ***TreeDP***
>   - 我们在利用后序遍历进行递归时会接受到左右子树处理好的信息，对于当前节点root，我们分析一下怎么构建它的信息类：
>     - 左子树或右子树其中一个发现了答案。
>     - 左子树和右子树各发现了 `p` 和 `q` 中的一个。
>     - `root` 是 `p` 或者 `q` 中的一个，左子树或者右子树发现了另外一个。
>     - 否则的话就是子树上没有答案，或者是整棵树上没有p或者q，或者是 `p` 和 `q` 都没有。
>   - 针对这些我们对子树要求 `3` 个信息，找到 `p` 否，找到 `q` 否，找到答案否，然后构建递归。
>   - 我们将信息整合成子树上找没找到 `p` 或者 `q` ，将答案抽离成全局变量，移除信息类，将其简洁化。
> - 两种算法的时间和空间复杂度都是 `O(N)` ，`N` 是二叉树的节点数。
> - ***基于二叉搜索树的性质一次查找***
>   - 由于 `p` 和 `q` 的大小知道，那么当 `p` 和 `q` 的值都大于 `root` 的值时，就去右子树上查找，反之去左子树上查找，当 `root` 的值等于 `p`或者 `q` 中的一个，或者 `p` 和 `q` 分别在 `root` 的两边时（即 `root` 的值在 `p` 和 `q` 中间）， `root` 就是最近公共祖先。
>   - 我们从根节点开始遍历：
>     - 如果当前节点的值大于 `p` 和 `q` 的值，说明 `p` 和 `q` 应该在当前节点的左子树，因此将当前节点移动到它的左子节点。
>     - 如果当前节点的值小于 `p` 和 `q` 的值，说明 `p` 和 `q` 应该在当前节点的右子树，因此将当前节点移动到它的右子节点。
>     - 如果当前节点的值不满足上述两条要求，那么说明当前节点就是分岔点。此时， `p` 和 `q` 要么在当前节点的不同的子树中，要么其中一个就是当前节点。
>   - 时间复杂度是 `O(N)` ，`N` 是二叉树的节点数，空间复杂度是 `O(1)` 。

```java
class TreeNode {
    
    int val;
    TreeNode left;
    TreeNode right;
    
    TreeNode() {}
    
    TreeNode(int val) {this.val = val;}
    
    TreeNode(int val, TreeNode left, TreeNode right) {
        this.val = val;
        this.left = left;
        this.right = right;
    }
    
}

class Solution {
    
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        // 迭代
        // return lowestCommonAncestorByIteration(root, p, q);
        // 递归
        // return lowestCommonAncestorByRecursion(root, p, q).ans;
        // 精简版
        /*dfs(root, p, q);
        return ans;*/
        // 利用二叉搜素树的性质
        return lowestCommonAncestorByAVL(root, p, q);
    }
    
    // 利用二叉搜素树的性质直接查找
    private TreeNode lowestCommonAncestorByAVL(TreeNode root, TreeNode p, TreeNode q) {
        // 利用二叉搜索树的性质：小于的在左边，大于的在右边，我们可以迅速判断两个节点的大概位置
        if (root == null) {
            return null;
        }
        while (true) {
            // 在当前节点的左子树上
            if (p.val < root.val && q.val < root.val) {
                root = root.left;
            } else if (p.val > root.val && q.val > root.val) {
                // 在当前节点的右子树上
                root = root.right;
            } else {
                // 如果它分别在当前节点的左右子树上，或者自己是其中一个，那就是了
                break;
            }
        }
        return root;
    }
    
    private TreeNode lowestCommonAncestorByIteration(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null) {
            return null;
        }
        // value是key的父亲
        HashMap<TreeNode, TreeNode> fatherMap = new HashMap<>();
        fatherMap.put(root, null);
        // 利用一个递归函数遍历所有的节点将每一个节点与其父节点加入到HashMap中
        fillFatherMap(root, fatherMap);
        // 找到p的祖先路径，直到遇到根节点为止，全部存在一个Set中
        HashSet<TreeNode> pFatherSet = new HashSet<>();
        pFatherSet.add(p);
        while (fatherMap.get(p) != null) {
            p = fatherMap.get(p);
            pFatherSet.add(p);
        }
        // 遍历q的所有祖先，有一个在p的祖先Set中，那么第一次找到的那个就是最近公共祖先
        while (!pFatherSet.contains(q)) {
            q = fatherMap.get(q);
        }
        return q;
    }
    
    private void fillFatherMap(TreeNode root, HashMap<TreeNode, TreeNode> fatherMap) {
        // 子节点不为空就加入子节点与自己
        if (root.left != null) {
            fatherMap.put(root.left, root);
            fillFatherMap(root.left, fatherMap);
        }
        if (root.right != null) {
            fatherMap.put(root.right, root);
            fillFatherMap(root.right, fatherMap);
        }
    }
    
    // 信息类，包括了子树上有没有找到p节点和q节点以及p节点和q节点有没有在子树上就找到公共祖先
    private static class Info {
        
        public boolean findP;
        public boolean findQ;
        public TreeNode ans;
        
        public Info(boolean findP, boolean findQ, TreeNode ans) {
            this.findP = findP;
            this.findQ = findQ;
            this.ans = ans;
        }
        
    }
    
    // 递归
    private Info lowestCommonAncestorByRecursion(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null) {
            return new Info(false, false, null);
        }
        // 获取信息
        Info leftInfo = lowestCommonAncestorByRecursion(root.left, p, q);
        Info rightInfo = lowestCommonAncestorByRecursion(root.right, p, q);
        // 处理信息
        // 自己是pq中的一个或者子树上有pq，那么自己为根的整棵树上就发现了pq
        boolean findP = (root == p) || leftInfo.findP || rightInfo.findP;
        boolean findQ = (root == q) || leftInfo.findQ || rightInfo.findQ;
        // 假设没找到先
        // 满足以下六种情况就算找到
        TreeNode ans = null;
        // 首先是汇聚节点与root无关
        // pq都在左子树中，两者的最近公共祖先在左树上
        if (leftInfo.ans != null) {
            ans = leftInfo.ans;
        } else if (rightInfo.ans != null) {
            // 同理，最近公共祖先在右树上
            ans = rightInfo.ans;
        } else if (findP && findQ) {
            // 汇聚节点与root有关
            // 包括同时找到了两个节点但汇聚节点不在子树上，那么root必然是pq中的一个，pq自己可以作为自己的祖先
            // 还有就是pq分别在不同的子树上，那么root就是最近公共祖先
            ans = root;
        }
        // 还有一种情况是以root为根节点的整棵树没找全pq节点，那自然ans为null
        return new Info(findP, findQ, ans);
    }
    
    // 答案
    private TreeNode ans = null;
    
    // DFS
    private boolean dfs(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null) {
            return false;
        }
        // 左右子树上有没有找到p或者q节点
        boolean leftHasPOrQ = dfs(root.left, p, q);
        boolean rightHasPOrQ = dfs(root.right, p, q);
        if ((leftHasPOrQ && rightHasPOrQ) || ((root.val == p.val || root.val == q.val) && (leftHasPOrQ || rightHasPOrQ))) {
            ans = root;
        }
        // 返回整棵树上有没有p节点或者q节点
        return leftHasPOrQ || rightHasPOrQ || root.val == p.val || root.val == q.val;
    }
    
}
```

> - ***Question 2: Tarjan算法 + 并查集***
>   - ![images](images/Tarjan算法与并查集解决二叉树节点间最近公共祖先的批量查询问题.jpg)
>   - 如果节点数 `n` ，查询数量 `m` ，过程总的时间复杂度 `O(n + m)` ，但是只能做批量离线查询。

```java
class Solution {
    
    // 离线批量查询最优解 -> Tarjan + 并查集
    // 如果有M条查询，时间复杂度O(N + M)
    // 但是必须把M条查询一次给全，不支持在线查询
    public static int[] query2(int[] father, int[][] queries) {
        int N = father.length;
        int M = queries.length;
        int[] help = new int[N];
        int head = 0;
        for (int i = 0; i < N; i++) {
            if (father[i] == i) {
                head = i;
            } else {
                help[father[i]]++;
            }
        }
        // 这是生成树的代码
        int[][] tree = new int[N][];
        for (int i = 0; i < N; i++) {
            tree[i] = new int[help[i]];
        }
        for (int i = 0; i < N; i++) {
            if (i != head) {
                tree[father[i]][--help[father[i]]] = i;
            }
        }
        // 查询数组转成另外一种形式
        for (int[] query : queries) {
            if (query[0] != query[1]) {
                help[query[0]]++;
                help[query[1]]++;
            }
        }
        int[][] mq = new int[N][];
        int[][] ansIndex = new int[N][];
        for (int i = 0; i < N; i++) {
            mq[i] = new int[help[i]];
            ansIndex[i] = new int[help[i]];
        }
        for (int i = 0; i < M; i++) {
            if (queries[i][0] != queries[i][1]) {
                mq[queries[i][0]][--help[queries[i][0]]] = queries[i][1];
                ansIndex[queries[i][0]][help[queries[i][0]]] = i;
                mq[queries[i][1]][--help[queries[i][1]]] = queries[i][0];
                ansIndex[queries[i][1]][help[queries[i][1]]] = i;
            }
        }
        int[] ans = new int[M];
        UnionFind unionFind = new UnionFind(N);
        recursion(head, tree, mq, ansIndex, unionFind, ans);
        for (int i = 0; i < M; i++) {
            if (queries[i][0] == queries[i][1]) {
                ans[i] = queries[i][0];
            }
        }
        return ans;
    }
    
    // 当前来到head点
    // mt是整棵树 head下方有哪些点 tree[head] = {a,b,c,d} head的孩子是abcd
    // mq问题列表 head有哪些问题 queries[head] = {x,y,z} (head，x) (head，y) (head z)
    // mi得到问题的答案，填在ans的什么地方 {6,12,34}
    // unionFind 并查集
    public static void recursion(int head, int[][] tree, int[][] queries, int[][] ansIndex, UnionFind unionFind, int[] ans) {
        for (int next : tree[head]) { // head有哪些孩子，都遍历去吧！
            recursion(next, tree, queries, ansIndex, unionFind, ans);
            unionFind.union(head, next);
            unionFind.setTag(head, head);
        }
        // 解决head的问题！
        int[] query = queries[head];
        int[] index = ansIndex[head];
        for (int k = 0; k < query.length; k++) {
            // head和谁有问题 query[k] 答案填哪 index[k]
            int tag = unionFind.getTag(query[k]);
            if (tag != -1) {
                ans[index[k]] = tag;
            }
        }
    }
    
    private static class UnionFind {
        
        private final int[] father; // father -> 并查集里面father信息，i -> i的father
        private final int[] size; // size[] -> 集合 --> i size[i]
        private final int[] tag; // tag[] -> 集合 ---> tag[i] = ?
        private final int[] stack; // 栈？并查集搞扁平化
        
        public UnionFind(int N) {
            father = new int[N];
            size = new int[N];
            tag = new int[N];
            stack = new int[N];
            for (int i = 0; i < N; i++) {
                father[i] = i;
                size[i] = 1;
                tag[i] = -1;
            }
        }
        
        private int find(int i) {
            int stackIndex = 0;
            while (i != father[i]) {
                stack[stackIndex++] = i;
                i = father[i];
            }
            while (stackIndex > 0) {
                stack[--stackIndex] = i;
            }
            return i;
        }
        
        public void union(int i, int j) {
            int father1 = find(i);
            int father2 = find(j);
            if (father1 != father2) {
                int size1 = size[father1];
                int size2 = size[father2];
                int big = size1 >= size2 ? father1 : father2;
                int small = big == father1 ? father2 : father1;
                father[small] = big;
                size[big] += size[small];
            }
        }
        
        // 集合的某个元素是i，请把整个集合打上统一的标签，tag
        public void setTag(int i, int tag) {
            this.tag[find(i)] = tag;
        }
        
        // 集合的某个元素是i，请把整个集合的tag信息返回
        public int getTag(int i) {
            return tag[find(i)];
        }
        
    }
    
}
```

> - ***Question 3: 树链剖分***
>   - 沿重链不断往上跳转查找即可，来到同一条重链时，谁的层级小谁就是公共祖先节点。
>   - 树链剖分代码查看以下章节：[树链剖分](../../../线段树SegmentTree/树链剖分.md)。

```java
class Solution {
    
    // 在线查询最优解 -> 树链剖分
    // 空间复杂度O(N), 支持在线查询，单次查询时间复杂度O(logN)
    // 如果有M次查询，时间复杂度O(N + M * logN)
    public static int[] query(int[] father, int[][] queries) {
        TreeChain treeChain = new TreeChain(father);
        int M = queries.length;
        int[] ans = new int[M];
        for (int i = 0; i < M; i++) {
            if (queries[i][0] == queries[i][1]) {
                ans[i] = queries[i][0];
            } else {
                ans[i] = treeChain.lca(queries[i][0], queries[i][1]);
            }
        }
        return ans;
    }
    
    public static class TreeChain {
        
        private int head;
        private int[][] tree;
        private int[] father;
        private int[] deep;
        private int[] son;
        private int[] childSize;
        private int[] top;
        
        public TreeChain(int[] father) {
            initTree(father);
            dfs1(head, 0);
            dfs2(head, head);
        }
        
        private void initTree(int[] father) {
            int n = father.length + 1;
            tree = new int[n][];
            this.father = new int[n];
            deep = new int[n];
            son = new int[n];
            childSize = new int[n];
            top = new int[n--];
            int[] cnum = new int[n];
            for (int i = 0; i < n; i++) {
                if (father[i] == i) {
                    head = i + 1;
                } else {
                    cnum[father[i]]++;
                }
            }
            tree[0] = new int[0];
            for (int i = 0; i < n; i++) {
                tree[i + 1] = new int[cnum[i]];
            }
            for (int i = 0; i < n; i++) {
                if (i + 1 != head) {
                    tree[father[i] + 1][--cnum[father[i]]] = i + 1;
                }
            }
        }
        
        private void dfs1(int u, int f) {
            father[u] = f;
            deep[u] = deep[f] + 1;
            childSize[u] = 1;
            int maxSize = -1;
            for (int v : tree[u]) {
                dfs1(v, u);
                childSize[u] += childSize[v];
                if (childSize[v] > maxSize) {
                    maxSize = childSize[v];
                    son[u] = v;
                }
            }
        }
        
        private void dfs2(int u, int t) {
            top[u] = t;
            if (son[u] != 0) {
                dfs2(son[u], t);
                for (int v : tree[u]) {
                    if (v != son[u]) {
                        dfs2(v, v);
                    }
                }
            }
        }
        
        public int lca(int a, int b) {
            a++;
            b++;
            while (top[a] != top[b]) {
                if (deep[top[a]] > deep[top[b]]) {
                    a = father[top[a]];
                } else {
                    b = father[top[b]];
                }
            }
            return (deep[a] < deep[b] ? a : b) - 1;
        }
        
    }
    
}
```

> - ***Question 4: 树上倍增递归版***
>   - 先让 `a` 和 `b` 往上跳到同一层，利用倍增算法找到a和b的最低公共祖先。
>   - 如果节点数为 `n` ，建立预处理结构的时间复杂度 `O(n * logn)` ，单次查询的时间复杂度 `O(log n)` ，优势是可以在线查询，如果一共 `m` 条查询，那么查询的总复杂度 `O(m * logn)` 。

```java
import java.io.*;
import java.util.Arrays;

public class Main {

    public static int MAXN = 500001;

    public static int LIMIT = 20;

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

    public static int[] next = new int[MAXN << 1];

    public static int[] to = new int[MAXN << 1];

    public static int cnt;

    // deep[i] : 节点i在第几层
    public static int[] deep = new int[MAXN];

    // stjump[i][p] : 节点i往上跳2的p次方步，到达的节点编号
    public static int[][] stjump = new int[MAXN][LIMIT];

    public static void build(int n) {
        power = log2(n);
        cnt = 1;
        Arrays.fill(head, 1, n + 1, 0);
    }

    public static void addEdge(int u, int v) {
        next[cnt] = head[u];
        to[cnt] = v;
        head[u] = cnt++;
    }

    // dfs递归版
    // 一般来说都这么写，但是本题附加的测试数据很毒
    // java这么写就会因为递归太深而爆栈，c++这么写就能通过
    public static void dfs(int u, int f) {
        deep[u] = deep[f] + 1;
        stjump[u][0] = f;
        for (int p = 1; p <= power; p++) {
            stjump[u][p] = stjump[stjump[u][p - 1]][p - 1];
        }
        for (int e = head[u]; e != 0; e = next[e]) {
            if (to[e] != f) {
                dfs(to[e], u);
            }
        }
    }

    public static int lca(int a, int b) {
        if (deep[a] < deep[b]) {
            int tmp = a;
            a = b;
            b = tmp;
        }
        for (int p = power; p >= 0; p--) {
            if (deep[stjump[a][p]] >= deep[b]) {
                a = stjump[a][p];
            }
        }
        if (a == b) {
            return a;
        }
        for (int p = power; p >= 0; p--) {
            if (stjump[a][p] != stjump[b][p]) {
                a = stjump[a][p];
                b = stjump[b][p];
            }
        }
        return stjump[a][0];
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        int n = (int) in.nval;
        in.nextToken();
        int m = (int) in.nval;
        in.nextToken();
        int root = (int) in.nval;
        build(n);
        for (int i = 1, u, v; i < n; i++) {
            in.nextToken();
            u = (int) in.nval;
            in.nextToken();
            v = (int) in.nval;
            addEdge(u, v);
            addEdge(v, u);
        }
        dfs(root, 0);
        for (int i = 1, a, b; i <= m; i++) {
            in.nextToken();
            a = (int) in.nval;
            in.nextToken();
            b = (int) in.nval;
            out.println(lca(a, b));
        }
        out.flush();
        out.close();
        br.close();
    }

}
```

> - ***Question 4: 树上倍增迭代版***

```java
import java.io.*;
import java.util.Arrays;

public class Main {

    public static int MAXN = 500001;

    public static int LIMIT = 20;

    public static int power;

    public static int log2(int n) {
        int ans = 0;
        while ((1 << ans) <= (n >> 1)) {
            ans++;
        }
        return ans;
    }

    public static int cnt;

    public static int[] head = new int[MAXN];

    public static int[] next = new int[MAXN << 1];

    public static int[] to = new int[MAXN << 1];

    public static int[][] stjump = new int[MAXN][LIMIT];

    public static int[] deep = new int[MAXN];

    public static void build(int n) {
        power = log2(n);
        cnt = 1;
        Arrays.fill(head, 1, n + 1, 0);
    }

    public static void addEdge(int u, int v) {
        next[cnt] = head[u];
        to[cnt] = v;
        head[u] = cnt++;
    }

    // dfs迭代版
    // nfe是为了实现迭代版而准备的栈
    public static int[][] ufe = new int[MAXN][3];

    public static int stackSize, u, f, e;

    public static void push(int u, int f, int e) {
        ufe[stackSize][0] = u;
        ufe[stackSize][1] = f;
        ufe[stackSize][2] = e;
        stackSize++;
    }

    public static void pop() {
        --stackSize;
        u = ufe[stackSize][0];
        f = ufe[stackSize][1];
        e = ufe[stackSize][2];
    }

    public static void dfs(int root) {
        stackSize = 0;
        // 栈里存放三个信息
        // u : 当前处理的点
        // f : 当前点u的父节点
        // e : 处理到几号边了
        // 如果e==-1，表示之前没有处理过u的任何边
        // 如果e==0，表示u的边都已经处理完了
        push(root, 0, -1);
        while (stackSize > 0) {
            pop();
            if (e == -1) {
                deep[u] = deep[f] + 1;
                stjump[u][0] = f;
                for (int p = 1; p <= power; p++) {
                    stjump[u][p] = stjump[stjump[u][p - 1]][p - 1];
                }
                e = head[u];
            } else {
                e = next[e];
            }
            if (e != 0) {
                push(u, f, e);
                if (to[e] != f) {
                    push(to[e], u, -1);
                }
            }
        }
    }

    public static int lca(int a, int b) {
        if (deep[a] < deep[b]) {
            int tmp = a;
            a = b;
            b = tmp;
        }
        for (int p = power; p >= 0; p--) {
            if (deep[stjump[a][p]] >= deep[b]) {
                a = stjump[a][p];
            }
        }
        if (a == b) {
            return a;
        }
        for (int p = power; p >= 0; p--) {
            if (stjump[a][p] != stjump[b][p]) {
                a = stjump[a][p];
                b = stjump[b][p];
            }
        }
        return stjump[a][0];
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        int n = (int) in.nval;
        in.nextToken();
        int m = (int) in.nval;
        in.nextToken();
        int root = (int) in.nval;
        build(n);
        for (int i = 1, u, v; i < n; i++) {
            in.nextToken();
            u = (int) in.nval;
            in.nextToken();
            v = (int) in.nval;
            addEdge(u, v);
            addEdge(v, u);
        }
        dfs(root);
        for (int i = 1, a, b; i <= m; i++) {
            in.nextToken();
            a = (int) in.nval;
            in.nextToken();
            b = (int) in.nval;
            out.println(lca(a, b));
        }
        out.flush();
        out.close();
        br.close();
    }

}
```

> - ***Question 4: Tarjan递归版***

```java
import java.io.*;
import java.util.Arrays;

public class Main {

    public static int MAXN = 500001;

    // 链式前向星建图
    public static int[] headEdge = new int[MAXN];

    public static int[] edgeNext = new int[MAXN << 1];

    public static int[] edgeTo = new int[MAXN << 1];

    public static int tcnt;

    // 每个节点有哪些查询，也用链式前向星方式存储
    public static int[] headQuery = new int[MAXN];

    public static int[] queryNext = new int[MAXN << 1];

    public static int[] queryTo = new int[MAXN << 1];

    // 问题的编号，一旦有答案可以知道填写在哪
    public static int[] queryIndex = new int[MAXN << 1];

    public static int qcnt;

    // 某个节点是否访问过
    public static boolean[] visited = new boolean[MAXN];

    // 并查集
    public static int[] father = new int[MAXN];

    // 收集的答案
    public static int[] ans = new int[MAXN];

    public static void build(int n) {
        tcnt = qcnt = 1;
        Arrays.fill(headEdge, 1, n + 1, 0);
        Arrays.fill(headQuery, 1, n + 1, 0);
        Arrays.fill(visited, 1, n + 1, false);
        for (int i = 1; i <= n; i++) {
            father[i] = i;
        }
    }

    public static void addEdge(int u, int v) {
        edgeNext[tcnt] = headEdge[u];
        edgeTo[tcnt] = v;
        headEdge[u] = tcnt++;
    }

    public static void addQuery(int u, int v, int i) {
        queryNext[qcnt] = headQuery[u];
        queryTo[qcnt] = v;
        queryIndex[qcnt] = i;
        headQuery[u] = qcnt++;
    }

    // 并查集找头节点递归版
    // 一般来说都这么写，但是本题附加的测试数据很毒
    // java这么写就会因为递归太深而爆栈，C++这么写就能通过
    public static int find(int i) {
        if (i != father[i]) {
            father[i] = find(father[i]);
        }
        return father[i];
    }

    // tarjan算法递归版
    // 一般来说都这么写，但是本题附加的测试数据很毒
    // java这么写就会因为递归太深而爆栈，C++这么写就能通过
    public static void tarjan(int u, int f) {
        visited[u] = true;
        for (int e = headEdge[u], v; e != 0; e = edgeNext[e]) {
            v = edgeTo[e];
            if (v != f) {
                tarjan(v, u);
                father[v] = u;
            }
        }
        for (int e = headQuery[u], v; e != 0; e = queryNext[e]) {
            v = queryTo[e];
            if (visited[v]) {
                ans[queryIndex[e]] = find(v);
            }
        }
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        int n = (int) in.nval;
        in.nextToken();
        int m = (int) in.nval;
        in.nextToken();
        int root = (int) in.nval;
        build(n);
        for (int i = 1, u, v; i < n; i++) {
            in.nextToken();
            u = (int) in.nval;
            in.nextToken();
            v = (int) in.nval;
            addEdge(u, v);
            addEdge(v, u);
        }
        for (int i = 1, u, v; i <= m; i++) {
            in.nextToken();
            u = (int) in.nval;
            in.nextToken();
            v = (int) in.nval;
            addQuery(u, v, i);
            addQuery(v, u, i);
        }
        tarjan(root, 0);
        for (int i = 1; i <= m; i++) {
            out.println(ans[i]);
        }
        out.flush();
        out.close();
        br.close();
    }

}
```

> - ***Question 4: Tarjan迭代版***

```java
import java.io.*;
import java.util.Arrays;

public class Main {

    public static int MAXN = 500001;

    public static int[] headEdge = new int[MAXN];

    public static int[] edgeNext = new int[MAXN << 1];

    public static int[] edgeTo = new int[MAXN << 1];

    public static int tcnt;

    public static int[] headQuery = new int[MAXN];

    public static int[] queryNext = new int[MAXN << 1];

    public static int[] queryTo = new int[MAXN << 1];

    public static int[] queryIndex = new int[MAXN << 1];

    public static int qcnt;

    public static boolean[] visited = new boolean[MAXN];

    public static int[] father = new int[MAXN];

    public static int[] ans = new int[MAXN];

    public static void build(int n) {
        tcnt = qcnt = 1;
        Arrays.fill(headEdge, 1, n + 1, 0);
        Arrays.fill(headQuery, 1, n + 1, 0);
        Arrays.fill(visited, 1, n + 1, false);
        for (int i = 1; i <= n; i++) {
            father[i] = i;
        }
    }

    public static void addEdge(int u, int v) {
        edgeNext[tcnt] = headEdge[u];
        edgeTo[tcnt] = v;
        headEdge[u] = tcnt++;
    }

    public static void addQuery(int u, int v, int i) {
        queryNext[qcnt] = headQuery[u];
        queryTo[qcnt] = v;
        queryIndex[qcnt] = i;
        headQuery[u] = qcnt++;
    }

    // 为了实现迭代版而准备的栈
    public static int[] stack = new int[MAXN];

    // 并查集找头节点迭代版
    public static int find(int i) {
        int size = 0;
        while (i != father[i]) {
            stack[size++] = i;
            i = father[i];
        }
        while (size > 0) {
            father[stack[--size]] = i;
        }
        return i;
    }

    // 为了实现迭代版而准备的栈
    public static int[][] ufe = new int[MAXN][3];

    public static int stackSize, u, f, e;

    public static void push(int u, int f, int e) {
        ufe[stackSize][0] = u;
        ufe[stackSize][1] = f;
        ufe[stackSize][2] = e;
        stackSize++;
    }

    public static void pop() {
        --stackSize;
        u = ufe[stackSize][0];
        f = ufe[stackSize][1];
        e = ufe[stackSize][2];
    }

    // 为了容易改成迭代版，修改一下递归版
    public static void tarjan(int u, int f) {
        visited[u] = true;
        for (int e = headEdge[u], v; e != 0; e = edgeNext[e]) {
            v = edgeTo[e];
            if (v != f) {
                tarjan(v, u);
                // 注意这里，注释了一行
//    father[v] = u;
            }
        }
        for (int e = headQuery[u], v; e != 0; e = queryNext[e]) {
            v = queryTo[e];
            if (visited[v]) {
                ans[queryIndex[e]] = find(v);
            }
        }
        // 注意这里，增加了一行
        father[u] = f;
    }

    // tarjan算法迭代版，根据上面的递归版改写
    public static void tarjan(int root) {
        stackSize = 0;
        // 栈里存放三个信息
        // u : 当前处理的点
        // f : 当前点u的父节点
        // e : 处理到几号边了
        // 如果e==-1，表示之前没有处理过u的任何边
        // 如果e==0，表示u的边都已经处理完了
        push(root, 0, -1);
        while (stackSize > 0) {
            pop();
            if (e == -1) {
                visited[u] = true;
                e = headEdge[u];
            } else {
                e = edgeNext[e];
            }
            if (e != 0) {
                push(u, f, e);
                if (edgeTo[e] != f) {
                    push(edgeTo[e], u, -1);
                }
            } else {
                // e == 0代表u后续已经没有边需要处理了
                for (int q = headQuery[u], v; q != 0; q = queryNext[q]) {
                    v = queryTo[q];
                    if (visited[v]) {
                        ans[queryIndex[q]] = find(v);
                    }
                }
                father[u] = f;
            }
        }
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        int n = (int) in.nval;
        in.nextToken();
        int m = (int) in.nval;
        in.nextToken();
        int root = (int) in.nval;
        build(n);
        for (int i = 1, u, v; i < n; i++) {
            in.nextToken();
            u = (int) in.nval;
            in.nextToken();
            v = (int) in.nval;
            addEdge(u, v);
            addEdge(v, u);
        }
        for (int i = 1, u, v; i <= m; i++) {
            in.nextToken();
            u = (int) in.nval;
            in.nextToken();
            v = (int) in.nval;
            addQuery(u, v, i);
            addQuery(v, u, i);
        }
        tarjan(root);
        for (int i = 1; i <= m; i++) {
            out.println(ans[i]);
        }
        out.flush();
        out.close();
        br.close();
    }

}
```

> - ***Question 1: Morris遍历***

```java
class Solution {

    // 提交以下的方法
    public static TreeNode lowestCommonAncestor(TreeNode head, TreeNode o1, TreeNode o2) {
        if (preOrder(o1.left, o1, o2) != null || preOrder(o1.right, o1, o2) != null) {
            return o1;
        }
        if (preOrder(o2.left, o1, o2) != null || preOrder(o2.right, o1, o2) != null) {
            return o2;
        }
        TreeNode left = preOrder(head, o1, o2);
        TreeNode cur = head;
        TreeNode mostRight = null;
        TreeNode lca = null;
        while (cur != null) {
            mostRight = cur.left;
            if (mostRight != null) {
                while (mostRight.right != null && mostRight.right != cur) {
                    mostRight = mostRight.right;
                }
                if (mostRight.right == null) {
                    mostRight.right = cur;
                    cur = cur.left;
                    continue;
                } else { // 第二次来到cur
                    mostRight.right = null;
                    if (lca == null) {
                        // 检查left是否在cur左树的右边界上
                        if (rightCheck(cur.left, left)) {
                            // 检查left看看右树里是否有o2
                            if (preOrder(left.right, o1, o2) != null) {
                                lca = left;
                            }
                            left = cur;
                            // 为什么此时检查的是left而不是cur
                            // 因为cur右树上的某些右指针可能没有恢复回来
                            // 需要等右指针恢复回来之后检查才不出错
                            // 所以此时检查的是left而不是cur
                            // 课上已经重点图解了
                        }
                    }
                }
            }
            cur = cur.right;
        }
        // 如果morris遍历结束了还没有收集到答案
        // 此时最后一个left还没有验证，它一定是答案
        return lca != null ? lca : left;
    }

    // 以head为头的树进行先序遍历，o1和o2谁先被找到就返回谁
    public static TreeNode preOrder(TreeNode head, TreeNode o1, TreeNode o2) {
        TreeNode cur = head;
        TreeNode mostRight = null;
        TreeNode ans = null;
        while (cur != null) {
            mostRight = cur.left;
            if (mostRight != null) {
                while (mostRight.right != null && mostRight.right != cur) {
                    mostRight = mostRight.right;
                }
                if (mostRight.right == null) {
                    if (ans == null && (cur == o1 || cur == o2)) {
                        ans = cur;
                    }
                    mostRight.right = cur;
                    cur = cur.left;
                    continue;
                } else {
                    mostRight.right = null;
                }
            } else {
                if (ans == null && (cur == o1 || cur == o2)) {
                    ans = cur;
                }
            }
            cur = cur.right;
        }
        return ans;
    }

    // 以head为头的树遍历右边界，返回是否找到了target
    public static boolean rightCheck(TreeNode head, TreeNode target) {
        while (head != null) {
            if (head == target) {
                return true;
            }
            head = head.right;
        }
        return false;
    }

}
```
