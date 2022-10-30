# 并查集相关代码模板

---

## *Java*

> - ***并查集的种类***
>   - 按照所用结构分可分为使用哈希表构建的并查集和使用数组构建的并查集
>   - 按照初始状态可以分为一次构建和动态加入构建
> - ***并查集的两大优化***
>   - 路径压缩（扁平化）
>   - 集合合并时小挂大
> - ***并查集的主要属性和方法***
>   - `nodes` 元素-节点对应关系哈希表
>   - `father` 并查集中的元素-所在集合代表节点哈希表
>   - `size` 并查集中的子集合-集合大小哈希表
>   - `sets()` 并查集中的总集合个数
>   - `find()` 找到一个元素所在集合的代表节点
>   - `union()` 合并两个元素
>   - `isInSameSet()` 两个元素是否在同一个集合中

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

---

> ***last change: 2022/10/28***

---
