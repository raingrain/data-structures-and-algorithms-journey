# [跳表]()

### *Java*

> - ***节点类实现跳表***

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

class SLNode<K extends Comparable<K>, V> {
    public K key;
    public V value;
    public ArrayList<SLNode<K, V>> nexts;

    public SLNode(K key, V value) {
        this.key = key;
        this.value = value;
        nexts = new ArrayList<SLNode<K, V>>();
    }

    // 将当前节点的key与传入的key作比较，判断是否小于传入key，是返回true反之false
    public boolean isKeyLess(K otherKey) {
        // 首先otherKey不能为空
        // 其次key可以为空也可以小于它
        return otherKey != null && (key == null || key.compareTo(otherKey) < 0);
    }

    // 将当前节点的key与传入的key作比较，判断是否等于传入key，是返回true反之false
    public boolean isKeyEqual(K otherKey) {
        // 两者都为空就相等
        // 或者都不为空且值相等
        return (key == null && otherKey == null) || (key != null && otherKey != null && key.compareTo(otherKey) == 0);
    }
}

class SkipList<K extends Comparable<K>, V> implements OrderedList<K, V> {

    // 跳表的核心，用概率打破输入上的不完美
    private static final double PROBABILITY = 0.5;
    private final SLNode<K, V> head;
    private int size;
    private int maxLevel;

    public SkipList() {
        // head里面不存储任何值
        head = new SLNode<K, V>(null, null);
        // 有一个0层链表指向空
        head.nexts.add(null);
        size = 0;
        maxLevel = 0;
    }

    // 在第level层中，从cur节点开始往后遍历，找到小于key的值最大节点返回
    private SLNode<K, V> mostRightLessNodeInLevel(K key, SLNode<K, V> cur, int level) {
        SLNode<K, V> next = cur.nexts.get(level);
        // 当下一个不为空且下一个小于时，cur前进
        // next有可能遇到空，也有可能找到相同的，也有可能遇到比key大的停
        while (next != null && next.isKeyLess(key)) {
            cur = next;
            next = cur.nexts.get(level);
        }
        // 返回cur
        return cur;
    }

    // 在整个跳表中在第1层找到小于key的最右节点
    // 从最高层向右下以阶梯式渐进查找
    private SLNode<K, V> mostRightLessNodeInTree(K key) {
        if (key == null) {
            return null;
        } else {
            int level = maxLevel;
            SLNode<K, V> cur = head;
            while (level >= 0) {
                cur = mostRightLessNodeInLevel(key, cur, level--);
            }
            return cur;
        }
    }

    @Override
    public int size() {
        return size;
    }

    @Override
    public boolean isEmpty() {
        return size == 0;
    }

    @Override
    public boolean containsKey(K key) {
        if (key == null) {
            return false;
        } else {
            SLNode<K, V> less = mostRightLessNodeInTree(key);
            SLNode<K, V> next = less.nexts.get(0);
            return next != null && next.isKeyEqual(key);
        }
    }

    @Override
    public void put(K key, V value) {
        if (key == null) {
            return;
        } else {
            // 找到整个表中小于key的最大节点less
            SLNode<K, V> less = mostRightLessNodeInTree(key);
            SLNode<K, V> next = less.nexts.get(0);
            // 看看less的下一个节点是不是key是就直接改值
            if (next != null && next.isKeyEqual(key)) {
                next.value = value;
            } else {
                // 不是就加进去
                size++;
                // 摇骰子，每次以0.5的概率决定进不进入更高一层
                int newSLNodeLevel = 0;
                while (Math.random() < PROBABILITY) {
                    newSLNodeLevel++;
                }
                // 如果新节点摇到的高度大于当前最高高度，在头节点处把高度往上升
                while (newSLNodeLevel > maxLevel) {
                    head.nexts.add(null);
                    maxLevel++;
                }
                // 建好新节点
                SLNode<K, V> newSLNode = new SLNode<>(key, value);
                // 将新节点的nexts数组全挂为空先，你的层数是多少你就挂多少
                for (int i = 0; i <= newSLNodeLevel; i++) {
                    newSLNode.nexts.add(null);
                }
                int level = maxLevel;
                SLNode<K, V> pre = head;
                while (level >= 0) {
                    pre = mostRightLessNodeInLevel(key, pre, level);
                    // 有可能我新进来的节点的高度小于当前表的最大高度
                    // 当遍历的高度降低到与newSLNodeLevel齐平时才开始往跳表中加数据
                    if (level <= newSLNodeLevel) {
                        // 我在这一层接管你在这一层的下一个
                        newSLNode.nexts.set(level, pre.nexts.get(level));
                        // 在这一层你接管我
                        pre.nexts.set(level, newSLNode);
                    }
                    // 往下面层遍历
                    level--;
                }
            }
        }
    }

    @Override
    public void remove(K key) {

    }

    @Override
    public V get(K key) {
        if (key == null) {
            return null;
        } else {
            SLNode<K, V> less = mostRightLessNodeInTree(key);
            SLNode<K, V> next = less.nexts.get(0);
            // 有才给你
            return next != null && next.isKeyEqual(key) ? next.value : null;
        }
    }

    @Override
    public K firstKey() {
        return head.nexts.get(0) != null ? head.nexts.get(0).key : null;
    }

    // 和mostRightLessNodeInLevel以及mostRightLessNodeInTree很像
    @Override
    public K lastKey() {
        int level = maxLevel;
        SLNode<K, V> cur = head;
        while (level >= 0) {
            SLNode<K, V> next = cur.nexts.get(level);
            while (next != null) {
                cur = next;
                next = cur.nexts.get(level);
            }
            level--;
        }
        return cur.key;
    }

    @Override
    public K floorKey(K key) {
        if (key == null) {
            return null;
        } else {
            SLNode<K, V> less = mostRightLessNodeInTree(key);
            SLNode<K, V> next = less.nexts.get(0);
            // 看看有没有下一个节点，且下一个节点等于key吗，否则就是空或者大于key，不会小于key，因为有mostRightLessNodeInTree()函数
            return next != null && next.isKeyEqual(key) ? next.key : less.key;
        }
    }

    @Override
    public K ceilingKey(K key) {
        if (key == null) {
            return null;
        } else {
            SLNode<K, V> less = mostRightLessNodeInTree(key);
            SLNode<K, V> next = less.nexts.get(0);
            // 找的是小于key的最右节点，那它的下一个节点必然是大于等于key的第一个
            return next != null ? next.key : null;
        }
    }

    // 跳表只能从0层开始有序查找
    // 且是按照key有序查找，而不是加入时的顺序
    @Override
    public K getIndexKey(int index) {
        if (index < 0 || size > index) {
            throw new RuntimeException("invalid parameter.");
        } else {
            SLNode<K, V> cur = head;
            K key = null;
            for (int i = index; i >= 0; i--) {
                cur = cur.nexts.get(0);
                key = cur.key;
            }
            return key;
        }
    }

    @Override
    public V getIndexValue(int index) {
        if (index < 0 || size > index) {
            throw new RuntimeException("invalid parameter.");
        } else {
            SLNode<K, V> cur = head;
            V value = null;
            for (int i = index; i >= 0; i--) {
                cur = cur.nexts.get(0);
                value = cur.value;
            }
            return value;
        }
    }
}

```
