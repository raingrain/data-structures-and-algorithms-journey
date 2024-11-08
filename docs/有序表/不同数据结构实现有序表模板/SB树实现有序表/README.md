# SB树实现有序表

## Java

> - **节点类实现SizeBalancedTree**
>   - SB树与AVL树都是具有平衡性的搜索二叉树，它们的不同在于：
>     - SB树的平衡条件不如AVL树严苛，对于一个节点来说，它只要保证它的儿子节点和孙子节点之间满足以叔叔节点为头的整棵树的节点数不小于任一以其侄子节点为头的整棵树的节点的条件即可。
>     - 两种树之间都是四种违规类型，SB树的违规看的是哪个侄子违规（即侄子树的节点数大于其叔叔树的节点数），例如左儿子的左儿子大于其右叔叔，那就是LL型违规，其他同理。
>     - 不同的违规类型，SB树的旋转与AVL树的相同，但SB树还会递归地调用 `keepBalance` 函数，即在一次 `keepBalance` 使得节点发生旋转后，再对受影响的节点（即子树变了）调用 `keepBalance` ，这种操作使得SB树可能一次改好多次但可能很久都不改，降低的操作频率（这种模糊平衡性的树如红黑树、B+树等有利于减少磁盘IO），即使雪崩时操作多其时间复杂度均摊下来也才`O(logN)` 。

```java
// 有序表接口
interface OrderedList<K extends Comparable<K>, V> {
    
    // 有序表的元素数量
    public int size();
    
    // 有序表是否为空
    public boolean isEmpty();
    
    // 有序表中有没有这个key
    public boolean containsKey(K key);
    
    // 往有序表中加入指定key
    public void put(K key, V value);
    
    // 在有序表中移除指定key
    public void remove(K key);
    
    // 返回指定key的value
    public V get(K key);
    
    // 找到有序表中索引为index的键
    public K getIndexKey(int index);
    
    // 找到有序表中索引为index的键对应的值
    public V getIndexValue(int index);
    
    // 返回有序表中的最小key
    public K minimumKey();
    
    // 返回有序表中的最大key
    public K maximumKey();
    
    // 返回有序表中小于或等于给定键的最大键，如果没有这样的键，则 null
    public K floorKey(K key);
    
    // 返回有序表中大于或等于给定键的最小键，如果没有这样的键，则 null
    public K ceilingKey(K key);
    
}

class SBNode<K extends Comparable<K>, V> {
    
    public K key;
    public V value;
    // 平衡因子：节点数
    public int size;
    public SBNode<K, V> left;
    public SBNode<K, V> right;
    
    public SBNode(K key, V value) {
        this.key = key;
        this.value = value;
        this.size = 1;
    }
    
}

class SBTree<K extends Comparable<K>, V> implements OrderedList<K, V> {
    
    private SBNode<K, V> root;
    
    public SBTree() {
        root = null;
    }
    
    // 获取一个树的节点数
    private int getSize(SBNode<K, V> head) {
        return 1 + (head.left == null ? 0 : head.left.size) + (head.right == null ? 0 : head.right.size);
    }
    
    // 左旋
    private SBNode<K, V> leftRotate(SBNode<K, V> head) {
        SBNode<K, V> right = head.right;
        head.right = right.left;
        right.left = head;
        // 没有加节点，整棵树的节点数不变
        right.size = head.size;
        // 孩子变了，节点数变了
        head.size = getSize(head);
        return right;
    }
    
    // 右旋
    private SBNode<K, V> rightRotate(SBNode<K, V> head) {
        SBNode<K, V> left = head.left;
        head.left = left.right;
        left.right = head;
        left.size = head.size;
        head.size = getSize(head);
        return left;
    }
    
    // 保持平衡性
    private SBNode<K, V> keepBalance(SBNode<K, V> head) {
        if (head == null) {
            return null;
        }
        int leftSize = head.left != null ? head.left.size : 0;
        int leftLeftSize = head.left != null && head.left.left != null ? head.left.left.size : 0;
        int leftRightSize = head.left != null && head.left.right != null ? head.left.right.size : 0;
        int rightSize = head.right != null ? head.right.size : 0;
        int rightLeftSize = head.right != null && head.right.left != null ? head.right.left.size : 0;
        int rightRightSize = head.right != null && head.right.right != null ? head.right.right.size : 0;
        // 调整动作与AVL树相同
        if (leftLeftSize > rightSize) {
            // LL
            head = rightRotate(head);
            // 孩子变了，递归地往下查平衡情况
            head.right = keepBalance(head.right);
            head = keepBalance(head);
        } else if (leftRightSize > rightSize) {
            // LR
            head.left = leftRotate(head.left);
            head = rightRotate(head);
            head.left = keepBalance(head.left);
            head.right = keepBalance(head.right);
            head = keepBalance(head);
        } else if (rightRightSize > leftSize) {
            // RR
            head = leftRotate(head);
            head.left = keepBalance(head.left);
            head = keepBalance(head);
        } else if (rightLeftSize > leftSize) {
            // RL
            head.right = rightRotate(head.right);
            head = leftRotate(head);
            head.left = keepBalance(head.left);
            head.right = keepBalance(head.right);
            head = keepBalance(head);
        }
        return head;
    }
    
    // 往SB树中添加节点
    private SBNode<K, V> add(SBNode<K, V> head, K key, V value) {
        if (head == null) {
            return new SBNode<>(key, value);
        }
        head.size++;
        if (key.compareTo(head.key) < 0) {
            head.left = add(head.left, key, value);
        } else {
            head.right = add(head.right, key, value);
        }
        // 加完后递归地往上查所有影响到的节点的平衡性
        return keepBalance(head);
    }
    
    // 在SB树中删除节点
    private SBNode<K, V> delete(SBNode<K, V> head, K key) {
        head.size--;
        if (key.compareTo(head.key) < 0) {
            head.left = delete(head.left, key);
        } else if (key.compareTo(head.key) > 0) {
            head.right = delete(head.right, key);
        } else {
            if (head.left == null && head.right == null) {
                head = null;
            } else if (head.left == null && head.right != null) {
                head = head.right;
            } else if (head.left != null && head.right == null) {
                head = head.left;
            } else {
                SBNode<K, V> pre = null;
                SBNode<K, V> rightTreeMostLeft = head.right;
                // 直接找到右树最左节点拉上来
                // pre用于记录右数最左节点的父节点
                rightTreeMostLeft.size--;
                while (rightTreeMostLeft.left != null) {
                    pre = rightTreeMostLeft;
                    rightTreeMostLeft = rightTreeMostLeft.left;
                    rightTreeMostLeft.size--;
                }
                // 右树只有一个节点的情况
                if (pre != null) {
                    pre.left = rightTreeMostLeft.right;
                    rightTreeMostLeft.right = head.right;
                }
                rightTreeMostLeft.left = head.left;
                rightTreeMostLeft.size = getSize(rightTreeMostLeft);
                head = rightTreeMostLeft;
            }
        }
        // 与AVL树差别最大的地方：可能很久都不需要调整，就算雪崩时的时间复杂度均摊也一样
        // head = maintain(head);
        return head;
    }
    
    // 在Sb树中找到key值距离给定key最接近的节点，可以相等
    private SBNode<K, V> findNearestNode(K key) {
        SBNode<K, V> pre = root;
        SBNode<K, V> cur = root;
        while (cur != null) {
            pre = cur;
            if (key.compareTo(cur.key) == 0) {
                break;
            } else if (key.compareTo(cur.key) < 0) {
                cur = cur.left;
            } else {
                cur = cur.right;
            }
        }
        return pre;
    }
    
    // 在Sb树中找到key值距离给定key最接近且大于等于给定key的节点，可以相等
    private SBNode<K, V> findNearestAndNoSmallNode(K key) {
        SBNode<K, V> ans = null;
        SBNode<K, V> cur = root;
        while (cur != null) {
            if (key.compareTo(cur.key) == 0) {
                ans = cur;
                break;
            } else if (key.compareTo(cur.key) < 0) {
                ans = cur;
                cur = cur.left;
            } else {
                cur = cur.right;
            }
        }
        return ans;
    }
    
    // 在Sb树中找到key值距离给定key最接近且小于等于给定key的节点，可以相等
    private SBNode<K, V> findNearestAndNoBigNode(K key) {
        SBNode<K, V> ans = null;
        SBNode<K, V> cur = root;
        while (cur != null) {
            if (key.compareTo(cur.key) == 0) {
                ans = cur;
                break;
            } else if (key.compareTo(cur.key) < 0) {
                cur = cur.left;
            } else {
                ans = cur;
                cur = cur.right;
            }
        }
        return ans;
    }
    
    // 在SB树中找到其实际对应的有序序列中第k位的值对于的节点
    // 调用该函数的函数确保k不越界
    private SBNode<K, V> getIndexNode(SBNode<K, V> head, int k) {
        if (k == (head.left == null ? 0 : head.left.size) + 1) {
            return head;
        } else if (k <= (head.left == null ? 0 : head.left.size)) {
            return getIndexNode(head.left, k);
        } else {
            return getIndexNode(head.right, k - (head.left == null ? 0 : head.left.size) - 1);
        }
    }
    
    @Override
    public int size() {
        return root == null ? 0 : root.size;
    }
    
    @Override
    public boolean isEmpty() {
        return root == null;
    }
    
    @Override
    public boolean containsKey(K key) {
        if (key == null) {
            throw new RuntimeException("invalid parameter.");
        }
        SBNode<K, V> nearestNode = findNearestNode(key);
        return nearestNode != null && key.compareTo(nearestNode.key) == 0;
    }
    
    @Override
    public void put(K key, V value) {
        if (key == null) {
            throw new RuntimeException("invalid parameter.");
        }
        SBNode<K, V> nearestNode = findNearestNode(key);
        if (nearestNode != null && key.compareTo(nearestNode.key) == 0) {
            nearestNode.value = value;
        } else {
            root = add(root, key, value);
        }
    }
    
    @Override
    public void remove(K key) {
        if (key == null) {
            throw new RuntimeException("invalid parameter.");
        }
        if (containsKey(key)) {
            root = delete(root, key);
        }
    }
    
    @Override
    public V get(K key) {
        if (key == null) {
            throw new RuntimeException("invalid parameter.");
        }
        SBNode<K, V> nearestNode = findNearestNode(key);
        return nearestNode != null && key.compareTo(nearestNode.key) == 0 ? nearestNode.value : null;
    }
    
    @Override
    public K getIndexKey(int index) {
        if (index < 0 || index > this.size()) {
            throw new RuntimeException("invalid parameter.");
        }
        // 在以root为头的整棵树上找index位置的节点，由于外部传入的是数组序而内部实现为了方便从一开始，所以要加1
        return getIndexNode(root, index + 1).key;
    }
    
    @Override
    public V getIndexValue(int index) {
        if (index < 0 || index > this.size()) {
            throw new RuntimeException("invalid parameter.");
        }
        return getIndexNode(root, index + 1).value;
    }
    
    @Override
    public K minimumKey() {
        if (root == null) {
            return null;
        }
        SBNode<K, V> cur = root;
        while (cur.left != null) {
            cur = cur.left;
        }
        return cur.key;
    }
    
    @Override
    public K maximumKey() {
        if (root == null) {
            return null;
        }
        SBNode<K, V> cur = root;
        while (cur.right != null) {
            cur = cur.right;
        }
        return cur.key;
    }
    
    @Override
    public K floorKey(K key) {
        if (key == null) {
            throw new RuntimeException("invalid parameter.");
        }
        SBNode<K, V> nearestAndNoBigNode = findNearestAndNoBigNode(key);
        return nearestAndNoBigNode == null ? null : nearestAndNoBigNode.key;
    }
    
    @Override
    public K ceilingKey(K key) {
        if (key == null) {
            throw new RuntimeException("invalid parameter.");
        }
        SBNode<K, V> nearestAndNoSmallNode = findNearestAndNoSmallNode(key);
        return nearestAndNoSmallNode == null ? null : nearestAndNoSmallNode.key;
    }
    
    public SBNode<K, V> getRoot() {
        return root;
    }
    
}
```
