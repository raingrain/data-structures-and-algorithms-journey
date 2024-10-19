# 数据流中的前k个高频元素

## [550 · 最常使用的K个单词II](https://www.lintcode.com/problem/top-k-frequent-words-ii/)

> - ***Question***
>   - 设计一个名为 `TopK` 的数据结构，包括一下两个方法：
>     - `void add(String str)` 接收数据流吐出来的字符串。
>     - `List<String> topK()` 返回包含前 `k` 个词频最高的字符串，词频相同按照字典序排序，多个字符串词频相同，选加入时间早的单词返回。
>   - 数据流会随机调用这两个方法，且初始时会告诉这个结构 `k` 是多少。

---

## *Java*

> - ***加强堆***

```java
class TopK {
    
    // 节点类
    private static class Node {
        
        // 字符串
        public String str;
        // 词频
        public int times;
        
        public Node(String str, int times) {
            this.str = str;
            this.times = times;
        }
        
    }
    
    // 堆元素比较强
    private static class Comp implements Comparator<Node> {
        
        @Override
        public int compare(Node o1, Node o2) {
            return o1.times != o2.times ? (o1.times - o2.times) : (o2.str.compareTo(o1.str));
        }
        
    }
    
    // 堆
    private final Node[] heap;
    // 堆大小
    private int heapSize;
    // 对应表
    private final HashMap<String, Node> strNodeMap;
    // 反向索引表
    private final HashMap<Node, Integer> indexMap;
    // 比较器
    private final Comp comp;
    // 有序表同步更新TopK，也可以选择每次调用topK函数时进行一次排序
    private final TreeSet<Node> treeSet;
    
    // 加入字符串
    public TopK(int k) {
        heap = new Node[k];
        heapSize = 0;
        strNodeMap = new HashMap<>();
        indexMap = new HashMap<>();
        comp = new Comp();
        treeSet = new TreeSet<>((o1, o2) -> o1.times != o2.times ? (o2.times - o1.times) : (o1.str.compareTo(o2.str)));
    }
    
    public void add(String str) {
        // 如果是Top0问题，那么堆为空，你怎么加字符串我都不管你
        if (heap.length == 0) {
            return;
        }
        // 当前加入字符串对应的节点
        Node node = null;
        // 当前加入字符串在堆数组中对应的索引
        int preIndex = -1;
        if (!strNodeMap.containsKey(str)) {
            // 这个字符串之前没加过
            // 新建节点
            node = new Node(str, 1);
            // 字符串节点对应表中加入
            strNodeMap.put(str, node);
            // 反向索引表中加入，-1表示不在堆中
            indexMap.put(node, -1);
        } else {
            // 这个字符串之前加过
            // 拿到节点
            node = strNodeMap.get(str);
            // 要在time++之前，先在treeSet中删掉
            // 原因是因为一但times++，curNode在treeSet中的排序就失效了
            // 这种失效会导致整棵treeSet出现问题
            // 即treeSet不是加强有序表
            /*if (treeSet.contains(node)) {
                treeSet.remove(node);
            }*/
            // remove可以判断treeSet中有没有这个节点
            treeSet.remove(node);
            // 词频++
            node.times++;
            // 获得在堆中索引
            preIndex = indexMap.get(node);
        }
        // 看看入堆的处理
        if (preIndex == -1) {
            // 首先是不在堆中
            if (heapSize == heap.length) {
                // 如果堆满了，就要看堆顶元素与加入元素的关系，只有堆顶元素严格小于加入元素才加，相等不管
                if (comp.compare(heap[0], node) < 0) {
                    // 移除堆顶
                    treeSet.remove(heap[0]);
                    // 加入元素
                    treeSet.add(node);
                    // 反向索引表改
                    indexMap.put(heap[0], -1);
                    indexMap.put(node, 0);
                    // 加入堆
                    heap[0] = node;
                    // 下沉
                    heapify(0);
                }
            } else {
                // 堆没有满
                // 加入元素
                treeSet.add(node);
                // 反向索引表更新
                indexMap.put(node, heapSize);
                // 入堆
                heap[heapSize] = node;
                // 我在堆底，上浮
                heapInsert(heapSize);
                // 最后更新heapSize
                heapSize++;
            }
        } else {
            // 元素之前就在堆里
            // 更新一下有序表
            treeSet.add(node);
            // 词频变大，看看能不能下沉
            heapify(preIndex);
        }
    }
    
    // 按顺序提取treeSet中节点对应的字符串
    public List<String> topK() {
        ArrayList<String> ans = new ArrayList<>();
        for (Node node : treeSet) {
            ans.add(node.str);
        }
        return ans;
    }
    
    // 从index位置开始上浮
    private void heapInsert(int index) {
        // 首先不是堆顶，有父
        while (index != 0) {
            // 父节点索引
            int father = (index - 1) / 2;
            if (comp.compare(heap[index], heap[father]) < 0) {
                // 小于父就交换并更新索引
                swap(index, father);
                index = father;
            } else {
                // 不比上面小，不用更新
                break;
            }
        }
    }
    
    // 从index位置开始下沉
    private void heapify(int index) {
        // 左孩子索引
        int left = index * 2 + 1;
        // 右孩子索引
        int right = index * 2 + 2;
        // 父与子三元组中最小元素索引
        int smallest = index;
        // 首先要有孩子才能下沉
        while (left < heapSize) {
            // 左孩子小于父，设置最小索引
            if (comp.compare(heap[left], heap[index]) < 0) {
                smallest = left;
            }
            // 有右孩子且右孩子小于最小索引，最小索引更新
            if (right < heapSize && comp.compare(heap[right], heap[smallest]) < 0) {
                smallest = right;
            }
            if (smallest != index) {
                // 父不是最小的，小根堆中要下沉
                // 交换位置
                swap(index, smallest);
                // 索引重新赋值
                index = smallest;
                left = index * 2 + 1;
                right = index * 2 + 2;
            } else {
                // 父是最小了，不用下沉
                break;
            }
        }
    }
    
    // 在堆中交换两个元素
    private void swap(int i, int j) {
        // 反向索引表交换
        indexMap.put(heap[i], j);
        indexMap.put(heap[j], i);
        // 堆数组交换
        Node temp = heap[i];
        heap[i] = heap[j];
        heap[j] = temp;
    }
    
}
```
