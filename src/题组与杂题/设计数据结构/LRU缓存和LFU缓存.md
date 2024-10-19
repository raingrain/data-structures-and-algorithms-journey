# LRU缓存和LFU缓存

## [146. LRU 缓存](https://leetcode.cn/problems/lru-cache/)

## [剑指 Offer II 031. 最近最少使用缓存](https://leetcode.cn/problems/OrIXps/)

## [面试题 16.25. LRU 缓存](https://leetcode.cn/problems/lru-cache-lcci/)

> - ***Question 1***
>   - 请你设计并实现一个满足 `LRU` （最近最少使用）缓存约束的数据结构。实现 `LRUCache` 类：
>     - `LRUCache(int capacity)` 以正整数作为容量 `capacity` 初始化 `LRU` 缓存。
>     - `int get(int key)` 如果关键字 `key` 存在于缓存中，则返回关键字的值，否则返回 `-1` 。
>     - `void put(int key, int value)` 如果关键字 `key` 已经存在，则变更其数据值 `value` ；如果不存在，则向缓存中插入该组 `key-value` 。如果插入操作导致关键字数量超过 `capacity` ，则应该逐出最久未使用的关键字。
>   - 函数 `get` 和 `put` 必须以 `O(1)` 的平均时间复杂度运行。
>   - ***tips:***
>     - `1 <= capacity <= 3000`
>     - `0 <= key <= 10000`
>     - `0 <= value <= 10^5`
>     - 最多调用 `2 * 10^5` 次 `get` 和 `put`

## [460. LFU 缓存](https://leetcode.cn/problems/lfu-cache/)

> - ***Question 2***
>   - 请你为最不经常使用（LFU）缓存算法设计并实现数据结构。实现 `LFUCache` 类：
>     - `LFUCache(int capacity)` - 用数据结构的容量 `capacity` 初始化对象。
>     - `int get(int key)` - 如果键 key 存在于缓存中，则获取键的值，否则返回 `-1` 。
>     - `void put(int key, int value)` - 如果键 `key` 已存在，则变更其值；如果键不存在，请插入键值对。当缓存达到其容量 `capacity` 时，则应该在插入新项之前，移除最不经常使用的项。在此问题中，当存在平局（即两个或更多个键具有相同使用频率）时，应该去除最近最久未使用的键。
>   - 为了确定最不常使用的键，可以为缓存中的每个键维护一个使用计数器 。使用计数最小的键是最久未使用的键。
>   - 当一个键首次插入到缓存中时，它的使用计数器被设置为 `1` （由于 `put` 操作）。对缓存中的键执行 `get` 或 `put` 操作，使用计数器的值将会递增。
> - 函数 `get` 和 `put` 必须以 `O(1)` 的平均时间复杂度运行。
>   - ***tips:***
>     - `0 <= capacity <= 10^4`
>     - `0 <= key <= 10^5`
>     - `0 <= value <= 10^9`
>     - 最多调用 `2 * 10^5` 次 `get` 和 `put` 方法

---

## *Java*

> - ***Question 1: 哈希表 + 双向链表***
>   - 哈希表查节点，节点直接位于双向链表中，并用双向链表维护操作早晚关系，这是核心。

```java
class LRUCache {
    
    private final MyLRUCache<Integer, Integer> cache;
    
    public LRUCache(int capacity) {
        cache = new MyLRUCache<>(capacity);
    }
    
    public int get(int key) {
        Integer ans = cache.get(key);
        return ans == null ? -1 : ans;
    }
    
    public void put(int key, int value) {
        cache.set(key, value);
    }
    
    // 泛型缓存
    private static class MyLRUCache<K, V> {
        
        // key是加进来的key，value是将key和value包裹住的双向链表节点
        private final HashMap<K, Node<K, V>> keyNodeMap;
        // 双向链表的顺序表示操作时间的先后，头是最早的尾是最晚的，每操作一条记录就要把对应节点放在链表尾
        // 因为时间点不是精确数据，我们可以用链表顺序来表示
        private final DoubleLinkedList<K, V> doubleLinkedList;
        // 容量，缓存加进来的记录超过了容量要删除最早的记录，即链表头
        private final int capacity;
        
        public MyLRUCache(int capacity) {
            keyNodeMap = new HashMap<>();
            doubleLinkedList = new DoubleLinkedList<>();
            this.capacity = capacity;
        }
        
        // 获取记录
        public V get(K key) {
            if (!keyNodeMap.containsKey(key)) {
                // 没有就返回空
                return null;
            }
            // 有就拿到
            Node<K, V> ans = keyNodeMap.get(key);
            // 操作了一次，时间点后移，移动到链表末尾
            doubleLinkedList.moveNodeToTail(ans);
            return ans.value;
        }
        
        // 更新或者加新的值
        public void set(K key, V value) {
            if (keyNodeMap.containsKey(key)) {
                // 之前存在，更新
                Node<K, V> node = keyNodeMap.get(key);
                node.value = value;
                // 操作，时间后移
                doubleLinkedList.moveNodeToTail(node);
            } else {
                // 新增
                Node<K, V> newNode = new Node<>(key, value);
                // 新节点加入到哈希表中
                keyNodeMap.put(key, newNode);
                // 新的节点是刚操作的，直接加到末尾
                doubleLinkedList.addNodeInTail(newNode);
                if (keyNodeMap.size() == capacity + 1) {
                    // 超过容量了，删除最早之前操作的缓存
                    removeMostUnusedCache();
                }
            }
        }
        
        // 移除最早的记录
        private void removeMostUnusedCache() {
            // 获得删除后的头节点
            Node<K, V> node = doubleLinkedList.removeHead();
            assert node != null;
            // 哈希表中删除记录
            keyNodeMap.remove(node.key);
        }
        
    }
    
    // 双向链表节点
    private static class Node<K, V> {
        
        public K key;
        public V value;
        public Node<K, V> last;
        public Node<K, V> next;
        
        public Node(K key, V value) {
            this.key = key;
            this.value = value;
        }
        
    }
    
    // 双向链表
    private static class DoubleLinkedList<K, V> {
        
        // 头节点和尾节点
        private Node<K, V> head;
        private Node<K, V> tail;
        
        public DoubleLinkedList() {
            head = null;
            tail = null;
        }
        
        // 现在来了一个新的node，请挂到尾巴上去
        public void addNodeInTail(Node<K, V> node) {
            if (node == null) {
                // node为空
                return;
            }
            if (head == null) {
                // 链表是空链表，当前是加入的第一个节点
                head = node;
                tail = node;
            } else {
                // 链表不是空链表，直接加到尾巴上
                tail.next = node;
                node.last = tail;
                tail = node;
            }
        }
        
        // 一定保证node在双向链表里
        // node原始的位置，左右重新连好，然后把node分离出来
        // 挂到整个链表的尾巴上
        public void moveNodeToTail(Node<K, V> node) {
            if (tail == node) {
                // 已经是尾巴了，不用调整
                return;
            }
            if (head == node) {
                // 要移动的是头节点
                // 头移动到下一位
                head = node.next;
                head.last = null;
            } else {
                // 要移动的是中间节点
                // 断开
                node.last.next = node.next;
                node.next.last = node.last;
            }
            // node移到尾巴上
            node.last = tail;
            node.next = null;
            tail.next = node;
            tail = node;
        }
        
        // 把头节点从双向链表中删除并返回
        public Node<K, V> removeHead() {
            if (head == null) {
                // 没有头节点
                return null;
            }
            Node<K, V> ans = head;
            if (head == tail) {
                // 链表中只有一个节点
                head = null;
                tail = null;
            } else {
                head = ans.next;
                ans.next = null;
                head.last = null;
            }
            return ans;
        }
        
    }
    
}
```

> - ***Question 2: 哈希表 + 二维双向链表***
>   - 用一个桶来表示出现了几次的节点，一个桶内是一个双向链表，里面是LRU缓存。桶和桶之间也是双向链表，第一个桶的头节点就是使用最少操作最早的节点。
>   - 主要是同时维护两个双向链表，很麻烦。

```java
class LFUCache {
    
    private final int capacity; // 缓存的大小限制，即K
    private int size; // 缓存目前有多少个节点
    private final HashMap<Integer, Node> records;// 表示key(Integer)由哪个节点(Node)代表
    private final HashMap<Node, NodeList> heads; // 表示节点(Node)在哪个桶(NodeList)里
    private NodeList headList; // 整个结构中位于最左的桶
    
    public LFUCache(int capacity) {
        this.capacity = capacity;
        size = 0;
        records = new HashMap<>();
        heads = new HashMap<>();
        headList = null;
    }
    
    public void put(int key, int value) {
        if (capacity == 0) {
            return;
        }
        if (records.containsKey(key)) {
            Node node = records.get(key);
            node.value = value;
            node.times++;
            NodeList curNodeList = heads.get(node);
            move(node, curNodeList);
        } else {
            if (size == capacity) {
                Node node = headList.tail;
                headList.deleteNode(node);
                modifyHeadList(headList);
                records.remove(node.key);
                heads.remove(node);
                size--;
            }
            Node node = new Node(key, value, 1);
            if (headList == null) {
                headList = new NodeList(node);
            } else {
                if (headList.head.times.equals(node.times)) {
                    headList.addNodeFromHead(node);
                } else {
                    NodeList newList = new NodeList(node);
                    newList.next = headList;
                    headList.last = newList;
                    headList = newList;
                }
            }
            records.put(key, node);
            heads.put(node, headList);
            size++;
        }
    }
    
    public int get(int key) {
        if (!records.containsKey(key)) {
            return -1;
        }
        Node node = records.get(key);
        node.times++;
        NodeList curNodeList = heads.get(node);
        move(node, curNodeList);
        return node.value;
    }
    
    // 节点的数据结构
    private static class Node {
        
        public Integer key;
        public Integer value;
        public Integer times; // 这个节点发生get或者set的次数总和
        public Node up; // 节点之间是双向链表所以有上一个节点
        public Node down;// 节点之间是双向链表所以有下一个节点
        
        public Node(int key, int value, int times) {
            this.key = key;
            this.value = value;
            this.times = times;
        }
        
    }
    
    private static class NodeList {
        
        public Node head; // 桶的头节点
        public Node tail; // 桶的尾节点
        public NodeList last; // 桶之间是双向链表所以有前一个桶
        public NodeList next; // 桶之间是双向链表所以有后一个桶
        
        public NodeList(Node node) {
            head = node;
            tail = node;
        }
        
        // 把一个新的节点加入这个桶，新的节点都放在顶端变成新的头部
        public void addNodeFromHead(Node node) {
            node.down = head;
            head.up = node;
            head = node;
        }
        
        // 判断这个桶是不是空的
        public boolean isEmpty() {
            return head == null;
        }
        
        // 删除node节点并保证node的上下环境重新连接
        public void deleteNode(Node node) {
            if (head == tail) {
                // 桶内只有一个节点
                head = null;
                tail = null;
            } else {
                if (node == head) {
                    head = node.down;
                    head.up = null;
                } else if (node == tail) {
                    tail = node.up;
                    tail.down = null;
                } else {
                    node.up.down = node.down;
                    node.down.up = node.up;
                }
            }
            node.up = null;
            node.down = null;
        }
        
    }
    
    // removeNodeList：刚刚减少了一个节点的桶
    // 这个函数的功能是，判断刚刚减少了一个节点的桶是不是已经空了。
    // 1）如果不空，什么也不做
    // 2)如果空了，removeNodeList还是整个缓存结构最左的桶(headList)。
    // 删掉这个桶的同时也要让最左的桶变成removeNodeList的下一个。
    // 3)如果空了，removeNodeList不是整个缓存结构最左的桶(headList)。
    // 把这个桶删除，并保证上一个的桶和下一个桶之间还是双向链表的连接方式
    // 函数的返回值表示刚刚减少了一个节点的桶是不是已经空了，空了返回true；不空返回false
    private boolean modifyHeadList(NodeList removeNodeList) {
        if (removeNodeList.isEmpty()) {
            if (headList == removeNodeList) {
                headList = removeNodeList.next;
                if (headList != null) {
                    headList.last = null;
                }
            } else {
                removeNodeList.last.next = removeNodeList.next;
                if (removeNodeList.next != null) {
                    removeNodeList.next.last = removeNodeList.last;
                }
            }
            return true;
        }
        return false;
    }
    
    // 函数的功能
    // node这个节点的次数+1了，这个节点原来在oldNodeList里。
    // 把node从oldNodeList删掉，然后放到次数+1的桶中
    // 整个过程既要保证桶之间仍然是双向链表，也要保证节点之间仍然是双向链表
    private void move(Node node, NodeList oldNodeList) {
        oldNodeList.deleteNode(node);
        // preList表示次数+1的桶的前一个桶是谁
        // 如果oldNodeList删掉node之后还有节点，oldNodeList就是次数+1的桶的前一个桶
        // 如果oldNodeList删掉node之后空了，oldNodeList是需要删除的，所以次数+1的桶的前一个桶，是oldNodeList的前一个
        NodeList preList = modifyHeadList(oldNodeList) ? oldNodeList.last : oldNodeList;
        // nextList表示次数+1的桶的后一个桶是谁
        NodeList nextList = oldNodeList.next;
        if (nextList == null) {
            // 是一个新的桶
            NodeList newList = new NodeList(node);
            if (preList != null) {
                preList.next = newList;
            }
            newList.last = preList;
            if (headList == null) {
                // 没有桶变有桶
                headList = newList;
            }
            // 加入记录
            heads.put(node, newList);
        } else {
            // 要在中间插桶
            if (nextList.head.times.equals(node.times)) {
                // 下一个桶就是要去的
                nextList.addNodeFromHead(node);
                // 记录加
                heads.put(node, nextList);
            } else {
                // 新建
                NodeList newList = new NodeList(node);
                // 有前一个桶
                if (preList != null) {
                    preList.next = newList;
                }
                // 连接
                newList.last = preList;
                newList.next = nextList;
                nextList.last = newList;
                if (headList == nextList) {
                    headList = newList;
                }
                heads.put(node, newList);
            }
        }
    }
    
}
```

---

> ***last change: 2023/4/9***

---
