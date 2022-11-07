# 基于自定义数据结构的图相关代码模板

---

## *Java*

> - ***图的相关属性以及算法***

```java
// 顶点类
class Node {
    
    // 节点值
    public int value;
    // 入度，有几个节点是父亲
    public int in;
    // 出度，有几个节点是儿子
    public int out;
    // 所有儿子节点集合
    public ArrayList<Node> nexts;
    // 所有以当前Node为出发节点的边
    public ArrayList<Edge> edges;
    
    public Node(int value) {
        this.value = value;
        this.in = 0;
        this.out = 0;
        this.nexts = new ArrayList<>();
        this.edges = new ArrayList<>();
    }
    
}

// 边类
class Edge {
    
    // 权重
    public int weight;
    // 边的出发节点
    public Node from;
    // 边的到达点
    public Node to;
    
    public Edge(int weight, Node from, Node to) {
        this.weight = weight;
        this.from = from;
        this.to = to;
    }
    
}

// 图类
class Graph {
    
    // 点集，外部传入的值与其对应的节点
    public HashMap<Integer, Node> nodes;
    // 边集
    public HashSet<Edge> edges;
    
    public Graph() {
        this.nodes = new HashMap<>();
        this.edges = new HashSet<>();
    }
    
}

// 图数据结构转换器
class GraphGenerator {
    
    // 边集转我的图
    public static Graph createGraphByEdgesetArray(int[][] matrix) {
        Graph graph = new Graph();
        for (int[] edgeSet : matrix) {
            int weight = edgeSet[0];
            int from = edgeSet[1];
            int to = edgeSet[2];
            if (!graph.nodes.containsKey(from)) {
                graph.nodes.put(from, new Node(from));
            }
            if (!graph.nodes.containsKey(to)) {
                graph.nodes.put(to, new Node(to));
            }
            Node fromNode = graph.nodes.get(from);
            Node toNode = graph.nodes.get(to);
            Edge newEdge = new Edge(weight, fromNode, toNode);
            fromNode.nexts.add(toNode);
            fromNode.edges.add(newEdge);
            ++fromNode.out;
            ++toNode.in;
            graph.edges.add(newEdge);
        }
        return graph;
    }
    
}

// 基于自定义数据结构的图相关算法
class GraphAlgorithms {
    
    public static List<Integer> bfs(Node start) {
        // 存储遍历结果的列表
        List<Integer> ans = new LinkedList<>();
        // 深度和宽度优先遍历一定要先选择一个开始节点
        if (start != null) {
            // 准备一个队列做宽度优先遍历
            Queue<Node> queue = new ArrayDeque<>();
            // 准备一个集合来存储已经遍历过的节点，避免死循环和多次遍历
            HashSet<Node> set = new HashSet<>();
            // 先把开始节点加入
            queue.add(start);
            set.add(start);
            while (!queue.isEmpty()) {
                // 弹出队列中的第一个元素
                Node node = queue.poll();
                ans.add(node.value);
                // System.out.println(node.val);
                // 把它的儿子节点们加入到队列中
                for (Node next : node.nexts) {
                    if (!set.contains(next)) {
                        // 必须是set中没有的即之前没有遍历过的才能加入队列，并加入set
                        set.add(next);
                        queue.offer(next);
                    }
                }
            }
        }
        return ans;
    }
    
    // 深度优先遍历就是一条路走到黑
    // 走不动了返回上一级
    public static List<Integer> dfs(Node start) {
        // 存储遍历结果的列表
        List<Integer> ans = new LinkedList<>();
        if (start != null) {
            // 栈对应着当前路径
            Deque<Node> stack = new ArrayDeque<>();
            // 存储走过的节点
            HashSet<Node> set = new HashSet<>();
            // 先加入开始节点
            stack.push(start);
            set.add(start);
            // 入栈就打印
            // System.out.println(start.value);
            ans.add(start.value);
            while (!stack.isEmpty()) {
                // 弹出栈中的第一个节点
                Node node = stack.pop();
                // 看看自自己的子节点
                for (Node next : node.nexts) {
                    // 按顺序加入没遍历过的节点
                    if (!set.contains(next)) {
                        // 把父子节点都加进去
                        stack.push(node);
                        stack.push(next);
                        set.add(next);
                        // 父节点的结果已经加过了
                        ans.add(next.value);
                        // System.out.println(next.value);
                        // 跳出for循环，往下走，找路径的下一个点
                        break;
                    }
                }
            }
        }
        return ans;
    }
    
    // 拓扑排序
    public static List<Node> topologicalSort(Graph graph) {
        // key为节点，value为入度
        HashMap<Node, Integer> inMap = new HashMap<>();
        // 只有入度为0的节点才能加入队列
        Queue<Node> zeroInQueue = new ArrayDeque<>();
        // 把所有节点加到入度表中
        for (Node node : graph.nodes.values()) {
            inMap.put(node, node.in);
            // 入度为0的节点加入队列中
            if (node.in == 0) {
                zeroInQueue.offer(node);
            }
        }
        // 存储拓扑排序的结果
        List<Node> ans = new LinkedList<>();
        // 当队列不为空时
        while (!zeroInQueue.isEmpty()) {
            // 弹出队列首个元素并加入到结果数组中
            Node node = zeroInQueue.poll();
            ans.add(node);
            // 消除该节点在图中对子节点的影响
            for (Node next : node.nexts) {
                // 出度表中子节点value减一
                inMap.put(next, inMap.get(next) - 1);
                // 入度为0的节点才能加入队列
                if (inMap.get(next) == 0) {
                    zeroInQueue.offer(next);
                }
            }
        }
        return ans;
    }
    
    // 点集的并查集
    private static class UnionFindSet {
        
        private final HashMap<Node, Node> father;
        private final HashMap<Node, Integer> size;
        
        public UnionFindSet(Collection<Node> nodes) {
            this.father = new HashMap<>();
            this.size = new HashMap<>();
            for (Node node : nodes) {
                father.put(node, node);
                size.put(node, 1);
            }
        }
        
        private Node find(Node node) {
            Deque<Node> stack = new ArrayDeque<>();
            while (node != father.get(node)) {
                stack.push(node);
                node = father.get(node);
            }
            while (!stack.isEmpty()) {
                father.put(stack.pop(), node);
            }
            return node;
        }
        
        public boolean isInSameSet(Node a, Node b) {
            return find(a) == find(b);
        }
        
        public void union(Node a, Node b) {
            if (a == null || b == null) {
                return;
            }
            Node AHead = find(a);
            Node BHead = find(b);
            int ASize = size.get(AHead);
            int BSize = size.get(BHead);
            if (ASize != BSize) {
                if (ASize > BSize) {
                    father.put(BHead, AHead);
                    size.put(AHead, ASize + BSize);
                    size.remove(BHead);
                } else {
                    father.put(AHead, BHead);
                    size.put(BHead, ASize + BSize);
                    size.remove(AHead);
                }
            }
        }
        
    }
    
    // 返回最小生成树的边
    // 图只能是无向有权图
    // 最小生成树不需要头节点
    // 并查集+小根堆是一种贪心的思想，即逐项选择权重最小的边并保证没有环路，然后加入到生成树的边的集合
    // 优化看点被选完了就结束
    public static Set<Edge> MSTByKruskal(Graph graph) {
        // 创建点集的并查集
        UnionFindSet unionFindSet = new UnionFindSet(graph.nodes.values());
        // 创建小根堆
        // 排序规则为边的权重
        PriorityQueue<Edge> edgePriorityQueue = new PriorityQueue<>(Comparator.comparingInt(o -> o.weight));
        edgePriorityQueue.addAll(graph.edges);
        Set<Edge> ans = new HashSet<>();
        while (!edgePriorityQueue.isEmpty()) {
            // 弹出小根堆的堆顶
            Edge edge = edgePriorityQueue.poll();
            // 如果这条边的from和to不是在一个集合中，也就是这两个节点和这条边还没有被用于建树
            // 如果是那就证明被用来建树了，当前选择的边会将这两个节点构成环路（并查集的作用）
            if (!unionFindSet.isInSameSet(edge.from, edge.to)) {
                // 结果中加入边
                ans.add(edge);
                // 合并
                unionFindSet.union(edge.from, edge.to);
            }
        }
        return ans;
    }
    
    // prim是随机选取一个出发点，并采用点解锁边，边解锁点，点再解锁边的方式，每次都选取不会形成闭环且权重最小的边，直到所有点都被选中
    // 判断点有没有被选过依旧使用HashSet
    public static Set<Edge> MSTByPrim(Graph graph) {
        // 小根堆用于存储已经解锁的边
        PriorityQueue<Edge> edgePriorityQueue = new PriorityQueue<>(Comparator.comparingInt(o -> o.weight));
        // 存储解锁过的点的集合，即被用来建树的点集
        HashSet<Node> nodeSet = new HashSet<>();
        // 被用来建树的边
        Set<Edge> ans = new HashSet<>();
        // 随机地从图中的一个点开始
        for (Node node : graph.nodes.values()) {
            // 如果点集中不包含node这个点，就证明没加过
            if (!nodeSet.contains(node)) {
                nodeSet.add(node);
                // 解锁这个节点的所有边
                edgePriorityQueue.addAll(node.edges);
                while (!edgePriorityQueue.isEmpty()) {
                    // 弹出解锁的边中权重最小的边
                    Edge edge = edgePriorityQueue.poll();
                    // 一个新的可能的点
                    Node toNode = edge.to;
                    // 点集有了说明当前路与之前所选择的形成了闭环
                    if (!nodeSet.contains(toNode)) {
                        nodeSet.add(toNode);
                        ans.add(edge);
                        // 加入发现的二级边
                        // 本数据结构表示无向图的话，这里会重复加入edge
                        // 但下次找到edge时，由于它的终止点（node）加入了nodeSet，它会直接被跳过
                        edgePriorityQueue.addAll(toNode.edges);
                    }
                }
            }
            // 不break可以防止森林
            // break;
        }
        return ans;
    }
    
    private static Node getMinDistanceAndUnselectedNode(HashMap<Node, Integer> distanceMap, HashSet<Node> selectedNodes) {
        Node minNode = null;
        int minDistance = Integer.MAX_VALUE;
        for (Map.Entry<Node, Integer> entry : distanceMap.entrySet()) {
            Node node = entry.getKey();
            int distance = entry.getValue();
            if (!selectedNodes.contains(node) && distance < minDistance) {
                minNode = node;
                minDistance = distance;
            }
        }
        return minNode;
    }
    
    // 最短路径
    public static HashMap<Node, Integer> dijkstra(Node start) {
        // 结果表
        HashMap<Node, Integer> distanceMap = new HashMap<>();
        distanceMap.put(start, 0);
        // 已经考虑过的集合
        HashSet<Node> selectedNodes = new HashSet<>();
        // 在距离表中找到距离最小的且不在已经考虑过的集合中的点
        Node minNode = getMinDistanceAndUnselectedNode(distanceMap, selectedNodes);
        while (minNode != null) {
            int distance = distanceMap.get(minNode);
            // 更新后续点在距离表中的记录
            for (Edge edge : minNode.edges) {
                Node toNode = edge.to;
                // distanceMap.put(toNode, Math.min(distanceMap.getOrDefault(toNode, Integer.MAX_VALUE), distance + edge.weight));
                // 新点就直接加
                if (!distanceMap.containsKey(toNode)) {
                    distanceMap.put(toNode, distance + edge.weight);
                } else {
                    // 有过比个大小再加
                    distanceMap.put(toNode, Math.min(distanceMap.get(toNode), distance + edge.weight));
                }
            }
            // 考虑过了锁住这个点
            selectedNodes.add(minNode);
            // 找下一个点
            minNode = getMinDistanceAndUnselectedNode(distanceMap, selectedNodes);
        }
        return distanceMap;
    }
    
    // 返回一个节点以及到它的最短距离
    private static class Info {
        
        public Node node;
        public int distance;
        
        public Info(Node node, int distance) {
            this.node = node;
            this.distance = distance;
        }
        
    }
    
    // 加强堆
    private static class Heap {
        
        // 堆
        private final Node[] heap;
        // 反向索引表，即节点在堆中的哪个位置
        // 值为-1表示这个点进来过堆但是已经被弹出
        // 没有进入过堆的节点不会在这张表中有记录
        private final HashMap<Node, Integer> indexMap;
        // 距离表
        // 点当前的从出发点到其的最短距离
        private final HashMap<Node, Integer> distanceMap;
        // 堆大小
        private int heapSize;
        
        public Heap(int heapSize) {
            this.heapSize = heapSize;
            this.heap = new Node[heapSize];
            this.indexMap = new HashMap<>();
            this.distanceMap = new HashMap<>();
        }
        
        // 删除一个点时反向索引表不删除而是把值变成-1，所以不管有没有被移出堆都算进来过
        private boolean isEntered(Node node) {return indexMap.containsKey(node);}
        
        // 进来过而且没被移出堆就算在堆里面
        private boolean inHeap(Node node) {
            return isEntered(node) && indexMap.get(node) != -1;
        }
        
        // 交换
        private void swap(int i, int j) {
            // 反向索引表交换
            indexMap.put(heap[i], j);
            indexMap.put(heap[j], i);
            // 堆交换
            Node node = heap[i];
            heap[i] = heap[j];
            heap[j] = node;
        }
        
        // 正常insert，谁距离小谁上升
        private void heapInsert(int index) {
            while (distanceMap.get(heap[index]) < distanceMap.get(heap[(index - 1) / 2])) {
                swap(index, (index - 1) / 2);
                index = (index - 1) / 2;
            }
        }
        
        // 谁距离大谁下降
        private void heapIFY(int index) {
            int left = index * 2 + 1;
            while (left < heapSize) {
                int smallest = left + 1 < heapSize && distanceMap.get(heap[left + 1]) < distanceMap.get(heap[left]) ? left + 1 : left;
                smallest = distanceMap.get(heap[smallest]) < distanceMap.get(heap[index]) ? smallest : index;
                if (smallest == index) {
                    break;
                } else {
                    swap(index, smallest);
                    index = smallest;
                    left = index * 2 + 1;
                }
            }
        }
        
        // index位置的元素发生了变动
        private void resign(Node node) {
            heapInsert(indexMap.get(node));
            heapIFY(indexMap.get(node));
        }
        
        // 是否为空
        public boolean isEmpty() {
            return heapSize == 0;
        }
        
        // 新增
        // 修改
        // 加过且已经被移出堆
        public void addOrUpdateOrIgnore(Node node, int distance) {
            // 修改堆中元素
            if (inHeap(node)) {
                distanceMap.put(node, Math.min(distanceMap.get(node), distance));
                // 只可能小于等于，只insert也可以
                heapInsert(indexMap.get(node));
                // resign(node);
            }
            // 新增的元素
            if (!isEntered(node)) {
                // 直接加到堆底
                heap[heapSize] = node;
                // 反向索引表和距离表中添加记录
                indexMap.put(node, heapSize);
                distanceMap.put(node, distance);
                // 上溯调整堆
                heapInsert(heapSize++);
            }
        }
        
        // 弹出并删除堆顶
        public Info pop() {
            // 记录信息并返回
            Info info = new Info(heap[0], distanceMap.get(heap[0]));
            // 交换
            swap(0, heapSize - 1);
            // 删除一个点时反向索引表不删除而是把值变成-1
            indexMap.put(heap[heapSize - 1], -1);
            // 距离表中不保留
            distanceMap.remove(heap[heapSize - 1]);
            // 在堆中删除
            heap[heapSize - 1] = null;
            // 大小减一
            --heapSize;
            // 堆顶元素下沉
            heapIFY(0);
            return info;
        }
        
    }
    
    // 通过加强堆实现
    public static HashMap<Node, Integer> dijkstraByHeap(Node start, int size) {
        // 堆
        HashMap<Node, Integer> distanceMap = new HashMap<>();
        // 把出发节点加入
        Heap heap = new Heap(size);
        // 结果表
        heap.addOrUpdateOrIgnore(start, 0);
        while (!heap.isEmpty()) {
            // 弹出堆中的最短路径
            Info info = heap.pop();
            Node node = info.node;
            int distance = info.distance;
            // 把解锁出来的所有边加入堆中看看
            for (Edge edge : node.edges) {
                // 新的点的路径距离等于出发点到当前点的距离加上连接两点的边的权重
                heap.addOrUpdateOrIgnore(edge.to, edge.weight + distance);
            }
            // 加入结果
            distanceMap.put(node, distance);
        }
        return distanceMap;
    }
    
}
```

---

> ***last change: 2022/11/7***

---
