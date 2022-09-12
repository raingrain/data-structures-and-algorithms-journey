# [SB树]()

### *Java*

> - ***节点类实现SB树***
>   - SB树与AVL树都是具有平衡性的搜索二叉树，它们的不同在于：
>     - SB树的平衡条件不如AVL树严苛，对于一个节点来说，它只要保证它的儿子节点和孙子节点之间满足以叔叔节点为头的整棵树的节点数要大于以其任一侄子节点为头的整棵树的节点的条件即可，
>     - 两种树之间都是四种违规类型，SB树的违规看的是那个侄子违规（即侄子树的节点数大于其叔叔树的节点数），例如左儿子的左儿子大于其右叔叔，那就是LL型违规，其他同理
>     - 不同的违规类型，SB树的旋转与AVL树的相同，但SB树还会递归地调用maintain，即在一次maintain使得节点发生旋转后，再对受影响的节点（即子树变了）调用maintain，这种操作使得SB树可能一次改好多次但可能很久都不改，降低的操作频率（这种模糊平衡性的树都有利于减少磁盘IO），即使雪崩时操作多其时间复杂度均摊下来也才O(logN)

```java
// 有序表接口
interface OrderedList<K extends Comparable<K>, V> {

    // 有序表大小
    public int size();

    // 有序表是否为空
    public boolean isEmpty();

    // 有序表中有没有这个key
    public boolean containsKey(K key);

    // 往有序表中加入键值对
    public void put(K key, V value);

    // 移除指定key
    public void remove(K key);

    // 获取指定key的value
    public V get(K key);

    // 找到有序表中最小的key
    public K firstKey();

    // 找到有序表中最大的key
    public K lastKey();

    // 返回小于或等于给定键的最大键，如果没有这样的键，则 null
    public K floorKey(K key);

    // 返回大于或等于给定键的最小键，如果没有这样的键，则 null
    public K ceilingKey(K key);

    // 找到有序表中索引为index的键
    public K getIndexKey(int index);

    // 找到有序表中索引为index的键对应的值
    public V getIndexValue(int index);
}


class SBNode<K extends Comparable<K>, V> {

    public K key;
    public V value;
    public SBNode<K, V> left;
    public SBNode<K, V> right;
    // 平衡因子：节点数
    public int size;

    public SBNode(K key, V value) {
        this.key = key;
        this.value = value;
        size = 1;
    }
}

class SBTree<K extends Comparable<K>, V> implements OrderedList<K, V> {

    private SBNode<K, V> root;

    public SBTree() {
        root = null;
    }

    private int getSize(SBNode<K, V> head) {
        return 1 + (head.left == null ? 0 : head.left.size) + (head.right == null ? 0 : head.right.size);
    }

    private SBNode<K, V> leftRotate(SBNode<K, V> head) {
        SBNode<K, V> right = head.right;
        head.right = right.left;
        right.left = head;
        right.size = head.size;
        head.size = getSize(head);
        return right;
    }

    private SBNode<K, V> rightRotate(SBNode<K, V> head) {
        SBNode<K, V> left = head.left;
        head.left = left.right;
        left.right = head;
        left.size = head.size;
        head.size = getSize(head);
        return left;
    }

    private SBNode<K, V> maintain(SBNode<K, V> head) {
        if (head == null) {
            return null;
        } else {
            int leftSize = head.left != null ? head.left.size : 0;
            int leftLeftSize = head.left != null && head.left.left != null ? head.left.left.size : 0;
            int leftRightSize = head.left != null && head.left.right != null ? head.left.right.size : 0;
            int rightSize = head.right != null ? head.right.size : 0;
            int rightLeftSize = head.right != null && head.right.left != null ? head.right.left.size : 0;
            int rightRightSize = head.right != null && head.right.right != null ? head.right.right.size : 0;
            if (leftLeftSize > rightSize) {
                // LL型违规
                head = rightRotate(head);
                // 处理受影响的节点
                head.right = maintain(head.right);
                head = maintain(head);
            } else if (leftRightSize > rightSize) {
                // LR型违规
                head.left = leftRotate(head.left);
                head = rightRotate(head);
                // 处理受影响的节点
                head.left = maintain(head.left);
                head.right = maintain(head.right);
                head = maintain(head);
            } else if (rightRightSize > leftSize) {
                // RR型违规
                head = leftRotate(head);
                head.left = maintain(head.left);
                head = maintain(head);
            } else if (rightLeftSize > leftSize) {
                // RL型违规
                head.right = rightRotate(head.right);
                head = leftRotate(head);
                head.left = maintain(head.left);
                head.right = maintain(head.right);
                head = maintain(head);
            }
            return head;
        }
    }

    private SBNode<K, V> add(SBNode<K, V> head, K key, V value) {
        if (head == null) {
            return new SBNode<K, V>(key, value);
        } else {
            head.size++;
            if (key.compareTo(head.key) < 0) {
                head.left = add(head.left, key, value);
            } else {
                head.right = add(head.right, key, value);
            }
            return maintain(head);
        }
    }

    private SBNode<K, V> delete(SBNode<K, V> head, K key) {
        head.size--;
        if (key.compareTo(head.key) > 0) {
            head.right = delete(head.right, key);
        } else if (key.compareTo(head.key) < 0) {
            head.left = delete(head.left, key);
        } else {
            if (head.left == null && head.right == null) {
                head = null;
            } else if (head.left == null && head.right != null) {
                head = head.right;
            } else if (head.left != null && head.right == null) {
                head = head.left;
            } else { // 有左有右
                SBNode<K, V> pre = null;
                SBNode<K, V> rightMostLeft = head.right;
                // 把记录删除
                // 这里是直接找到最左节点拉上来，和AVL不同
                rightMostLeft.size--;
                while (rightMostLeft.left != null) {
                    pre = rightMostLeft;
                    rightMostLeft = rightMostLeft.left;
                    rightMostLeft.size--;
                }
                if (pre != null) {
                    pre.left = rightMostLeft.right;
                    rightMostLeft.right = head.right;
                }
                rightMostLeft.left = head.left;
                // 修改节点个数
                rightMostLeft.size = rightMostLeft.left.size + (rightMostLeft.right == null ? 0 : rightMostLeft.right.size) + 1;
                head = rightMostLeft;
            }
        }
        // 与AVL树差别最大的地方
        // 我可能很久都不需要调整，就算我雪崩我的时间复杂度均摊也一样
        // head = maintain(head);
        return head;
    }

    private SBNode<K, V> findLastIndex(K key) {
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

    private SBNode<K, V> findLastNoSmallIndex(K key) {
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

    private SBNode<K, V> findLastNoBigIndex(K key) {
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

    // 找到对于head为头的整棵搜索二叉树来说第k位置的节点
    // 调用的函数已经确保index不会越界
    private SBNode<K, V> getIndex(SBNode<K, V> head, int k) {
        if (k == (head.left == null ? 0 : head.left.size) + 1) {
            return head;
        } else if (k <= (head.left == null ? 0 : head.left.size)) {
            return getIndex(head.left, k);
        } else {
            return getIndex(head.right, k - (head.left == null ? 0 : head.left.size) - 1);
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
        } else {
            SBNode<K, V> lastNode = findLastIndex(key);
            return lastNode != null && key.compareTo(lastNode.key) == 0;
        }
    }

    @Override
    public void put(K key, V value) {
        if (key == null) {
            throw new RuntimeException("invalid parameter.");
        } else {
            SBNode<K, V> lastNode = findLastIndex(key);
            if (lastNode != null && key.compareTo(lastNode.key) == 0) {
                lastNode.value = value;
            } else {
                root = add(root, key, value);
            }
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
        } else {
            SBNode<K, V> lastNode = findLastIndex(key);
            if (lastNode != null && key.compareTo(lastNode.key) == 0) {
                return lastNode.value;
            } else {
                return null;
            }
        }
    }

    @Override
    public K firstKey() {
        if (root == null) {
            return null;
        } else {
            SBNode<K, V> cur = root;
            while (cur.left != null) {
                cur = cur.left;
            }
            return cur.key;
        }
    }

    @Override
    public K lastKey() {
        if (root == null) {
            return null;
        }
        {
            SBNode<K, V> cur = root;
            while (cur.right != null) {
                cur = cur.right;
            }
            return cur.key;
        }
    }

    @Override
    public K floorKey(K key) {
        if (key == null) {
            throw new RuntimeException("invalid parameter.");
        } else {
            SBNode<K, V> lastNoBigNode = findLastNoBigIndex(key);
            return lastNoBigNode == null ? null : lastNoBigNode.key;
        }
    }

    @Override
    public K ceilingKey(K key) {
        if (key == null) {
            throw new RuntimeException("invalid parameter.");
        } else {
            SBNode<K, V> lastNoSmallNode = findLastNoSmallIndex(key);
            return lastNoSmallNode == null ? null : lastNoSmallNode.key;
        }
    }

    @Override
    public K getIndexKey(int index) {
        // 确保index不会越界先
        if (index < 0 || index >= this.size()) {
            throw new RuntimeException("invalid parameter.");
        } else {
            // 在以root为头的整棵树上找index位置的节点，由于外部传入的是数组序而内部实现为了方便从一开始，所以要加1
            return getIndex(root, index + 1).key;
        }
    }

    @Override
    public V getIndexValue(int index) {
        if (index < 0 || index >= this.size()) {
            throw new RuntimeException("invalid parameter.");
        } else {
            return getIndex(root, index + 1).value;
        }
    }
}
```
