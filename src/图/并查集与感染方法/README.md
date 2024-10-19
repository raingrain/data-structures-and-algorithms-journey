# 并查集相关代码模板

## [并查集的实现](https://www.nowcoder.com/practice/e7ed657974934a30b2010046536a5372)

> - ***Question 1***
>   - 给定一个没有重复值的整形数组 `arr` ，初始时认为 `arr` 中每一个数各自都是一个单独的集合。请设计一种叫 `UnionFind` 的结构，并提供以下两个操作：
>     - `boolean isSameSet(int a, int b)` ：查询 `a` 和 `b` 这两个数是否属于一个集合。
>     - `void union(int a, int b)` ：把 `a` 所在的集合与 `b` 所在的集合合并在一起，原本两个集合各自的元素以后都算作同一个集合。
>   - ***输入描述***
>     - 第一行两个整数 `N, M` 。分别表示数组大小、操作次数。接下来 `M` 行，每行有一个整数 `opt` 。若 `opt = 1` ，后面有两个数 `x, y` ，表示查询 `(x, y)` 这两个数是否属于同一个集合。若 `opt = 2` ，后面有两个数 `x, y` ，表示把 `x, y` 所在的集合合并在一起。
>   - ***输出描述***
>     - 对于每个 `opt = 1` 的操作，若为真则输出Yes，否则输出No。
>   - ***tips:***
>     - `1 <= N, M <= 10^6`
>     - `1 <= x, y <= N`

## [P3367 【模板】并查集](https://www.luogu.com.cn/problem/P3367)

> - ***Question 2***
>   - 如题，现在有一个并查集，你需要完成合并和查询操作。
>   - ***输入描述***
>     - 第一行包含两个整数 `N, M` ，表示共有 `N` 个元素和 `M` 个操作。接下来 `M` 行，每行包含三个整数 `Zi, Xi, Yi` 。当 `Zi = 1` 时，将 `Xi` 与 `Yi` 所在的集合合并。当 `Zi = 2` 时，输出 `Xi` 与 `Yi` 是否在同一集合内，是的输出 `Y` ；否则输出 `N` 。
>   - ***输出描述***
>     - 对于每一个 `Zi = 2` 的操作，都有一行输出，每行包含一个大写字母，为 `Y` 或者 `N` 。
>   - ***tips:***
>     - `1 <= N <= 10^4`
>     - `1 <= M <= 2 * 10^5`
>     - `1 <= Xi, Yi <= N`
>     - `Zi = 1 or 2`

---

## *Java*

> - ***并查集的种类***
>   - 按照所用结构分可分为使用哈希表构建的并查集和使用数组构建的并查集
>   - 按照初始状态可以分为一次构建和动态加入构建
> - ***并查集的两大优化***
>   - 路径压缩（扁平化）
>   - 集合合并时小挂大
> - ***并查集的主要属性和方法***
>   - `HashMap<V, Node<V>> nodes` 元素-节点对应关系哈希表
>   - `HashMap<Node<V>, Node<V>> father` 并查集中的元素-所在集合代表节点哈希表
>   - `HashMap<Node<V>, Integer> size` 并查集中的子集合-集合大小哈希表
>   - `int sets()` 并查集中的总集合个数
>   - `Node<V> find(Node<V> node)` 找到一个元素所在集合的代表节点
>   - `void union(V a, V b)` 合并两个元素
>   - `boolean isInSameSet(V a, V b)` 两个元素是否在同一个集合中

```java
// 将元素包装成集合
class Node<V> {
    
    V value;
    
    public Node(V value) {
        this.value = value;
    }
    
}

// 哈希表实现简单并查集
class UnionFindSet<V> {
    
    // 节点集合
    private final HashMap<V, Node<V>> nodes;
    // 每一个节点的父亲
    private final HashMap<Node<V>, Node<V>> father;
    // 我们用代表节点来标注一个集合，sizeMap可以查询每一个代表节点对应的集合的大小
    private final HashMap<Node<V>, Integer> size;
    
    public UnionFindSet(List<V> values) {
        nodes = new HashMap<>();
        father = new HashMap<>();
        size = new HashMap<>();
        for (V value : values) {
            // 创建节点并加入到节点集合里面
            Node<V> node = new Node<>(value);
            nodes.put(value, node);
            // 最开始每一个节点自己就是一个集合，该集合的代表节点就是自己本身
            father.put(node, node);
            // 由上每个集合只有自己，大小为1
            size.put(node, 1);
        }
    }
    
    // 并查集里面有多少集合
    public int sets() {
        // 只有代表节点会在size中，所以size的长度决定了有多少个代表节点，也决定了有多少个集合
        return size.size();
    }
    
    // 查询代表节点并对路径进行扁平化处理
    private Node<V> find(Node<V> node) {
        // 准备一个栈来存储往上查找代表节点时所经过的节点
        Deque<Node<V>> pathStack = new ArrayDeque<>();
        // 如果你的父不是你自己，那么你就不是代表节点
        // 代表节点最重要的规则时它指针指向自己
        while (node != father.get(node)) {
            pathStack.push(node);
            node = father.get(node);
        }
        // 此时node必然是代表节点
        while (!pathStack.isEmpty()) {
            // 把所有经过的节点全部由重孙辈变成儿子辈
            father.put(pathStack.pop(), node);
        }
        return node;
    }
    
    // 合并两个节点所在集合
    public void union(V a, V b) {
        // 获得各自所在集合的代表节点
        Node<V> findA = find(nodes.get(a));
        Node<V> findB = find(nodes.get(b));
        // 如果代表节点不相同就代表需要合并，不是同一个集合
        if (findA != findB) {
            // 获得两个集合的大小
            int aSize = size.get(findA);
            int bSize = size.get(findB);
            if (aSize >= bSize) {
                // 短的整个集合整体变成长的儿子辈
                father.put(findB, findA);
                // 长的集合的长度变大
                size.put(findA, aSize + bSize);
                // size集合中移除短的集合
                size.remove(findB);
            } else {
                father.put(findA, findB);
                size.put(findB, aSize + bSize);
                size.remove(findA);
            }
        }
    }
    
    // 判断两个节点是不是在同一个集合中，只需要查询代表节点相不相同即可
    public boolean isInSameSet(V a, V b) {
        return find(nodes.get(a)) == find(nodes.get(b));
    }
    
}
```

> - ***Question 1***

```java
import java.io.*;

public class Main {

    public static int MAXN = 1000001;

    public static int[] father = new int[MAXN];

    public static int[] size = new int[MAXN];

    public static int[] stack = new int[MAXN];

    public static int n;

    public static void build() {
        for (int i = 0; i <= n; i++) {
            father[i] = i;
            size[i] = 1;
        }
    }

    // i号节点，往上一直找，找到代表节点返回！
    public static int find(int i) {
        // 沿途收集了几个点
        int size = 0;
        while (i != father[i]) {
            stack[size++] = i;
            i = father[i];
        }
        // 沿途节点收集好了，i已经跳到代表节点了
        while (size > 0) {
            father[stack[--size]] = i;
        }
        return i;
    }

    public static boolean isSameSet(int x, int y) {
        return find(x) == find(y);
    }

    public static void union(int x, int y) {
        int fx = find(x);
        int fy = find(y);
        if (fx != fy) {
            // fx是集合的代表：拿大小
            // fy是集合的代表：拿大小
            if (size[fx] >= size[fy]) {
                size[fx] += size[fy];
                father[fy] = fx;
            } else {
                size[fy] += size[fx];
                father[fx] = fy;
            }
        }
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            n = (int) in.nval;
            build();
            in.nextToken();
            int m = (int) in.nval;
            for (int i = 0; i < m; i++) {
                in.nextToken();
                int op = (int) in.nval;
                in.nextToken();
                int x = (int) in.nval;
                in.nextToken();
                int y = (int) in.nval;
                if (op == 1) {
                    out.println(isSameSet(x, y) ? "Yes" : "No");
                } else {
                    union(x, y);
                }
            }
        }
        out.flush();
        out.close();
        br.close();
    }

}
```

> - ***Question 2***

```java
import java.io.*;

public class Main {

    public static int MAXN = 10001;

    public static int[] father = new int[MAXN];

    public static int n;

    public static void build() {
        for (int i = 0; i <= n; i++) {
            father[i] = i;
        }
    }

    public static int find(int i) {
        if (i != father[i]) {
            father[i] = find(father[i]);
        }
        return father[i];
    }

    public static boolean isSameSet(int x, int y) {
        return find(x) == find(y);
    }

    public static void union(int x, int y) {
        father[find(x)] = find(y);
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            n = (int) in.nval;
            build();
            in.nextToken();
            int m = (int) in.nval;
            for (int i = 0; i < m; i++) {
                in.nextToken();
                int z = (int) in.nval;
                in.nextToken();
                int x = (int) in.nval;
                in.nextToken();
                int y = (int) in.nval;
                if (z == 1) {
                    union(x, y);
                } else {
                    out.println(isSameSet(x, y) ? "Y" : "N");
                }
            }
        }
        out.flush();
        out.close();
        br.close();
    }

}
```
