


```java
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
}

// AVL树的节点
class AVLNode<K extends Comparable<K>, V> {

    // 键
    public K key;
    // 值
    public V value;
    // 左右子树
    public AVLNode<K, V> left;
    public AVLNode<K, V> right;
    // 以该节点为头的树的高度
    public int height;

    public AVLNode(K key, V value) {
        this.key = key;
        this.value = value;
        this.height = 1;
    }
}

// 基于泛型实现的AVL树，key接口必须保证可比较
class AVLTree<K extends Comparable<K>, V> implements OrderedList<K, V> {

    private AVLNode<K, V> root;
    private int size;

    public AVLTree() {
        root = null;
        size = 0;
    }

    public int size() {
        return size;
    }

    public boolean isEmpty() {
        return size == 0;
    }

    // 获取一个节点为头时整棵树的高度
    private int getHeight(AVLNode<K, V> head) {
        return 1 + Math.max((head.left == null ? 0 : head.left.height), (head.right == null ? 0 : head.right.height));
    }

    // 右旋
    private AVLNode<K, V> rightRotate(AVLNode<K, V> head) {
        // 抓住要升上来的节点
        AVLNode<K, V> left = head.left;
        // node的左树变成要上升的节点的右树
        head.left = left.right;
        // 将node挂载到上升节点的右树上，实现了旋转
        left.right = head;
        // 在这次旋转中，由于node的左树和left的右树发生了变化，所以要检查它们的高度，它门各自的另外一颗子树并没有发生变化，不用检查
        head.height = getHeight(head);
        left.height = getHeight(left);
        // 把新局部树的节点返回
        return left;
    }

    // 左旋和右旋刚好相反
    private AVLNode<K, V> leftRotate(AVLNode<K, V> head) {
        AVLNode<K, V> right = head.right;
        head.right = right.left;
        right.left = head;
        head.height = getHeight(head);
        right.height = getHeight(right);
        return right;
    }

    // 违规性的检查
    private AVLNode<K, V> maintain(AVLNode<K, V> head) {
        if (head == null) {
            return null;
        } else {
            // 获取左右树的高度
            int leftHeight = head.left == null ? 0 : head.left.height;
            int rightHeight = head.right == null ? 0 : head.right.height;
            // 左右树高度差大于等于2就是违规
            if (Math.abs(leftHeight - rightHeight) > 1) {
                if (leftHeight > rightHeight) {
                    // 左树上左树的高度
                    int leftLeftHeight = head.left != null && head.left.left != null ? head.left.left.height : 0;
                    // 左树上右树的高度
                    int leftRightHeight = head.left != null && head.left.right != null ? head.left.right.height : 0;
                    // LL型违规，LL与LR型违规同时出现用LL，因为使用LR调整后可能还是违规
                    if (leftLeftHeight >= leftRightHeight) {
                        // 直接对整棵树进行右旋
                        head = rightRotate(head);
                    } else {
                        // 先对左树进行一次左旋
                        head.left = leftRotate(head.left);
                        // 再对整棵树进行一次右旋
                        head = rightRotate(head);
                    }
                } else {
                    // 右树与左树相反
                    int rightRightHeight = head.right != null && head.right.right != null ? head.right.right.height : 0;
                    int rightLeftHeight = head.right != null && head.right.left != null ? head.right.left.height : 0;
                    if (rightRightHeight >= rightLeftHeight) {
                        head = leftRotate(head);
                    } else {
                        head.right = rightRotate(head.right);
                        head = leftRotate(head);
                    }
                }
            }
            return head;
        }
    }

    // 不会撞到相同的key，相同的key直接更新
    // 增加key
    private AVLNode<K, V> add(AVLNode<K, V> head, K key, V value) {
        // 碰到空直接新建一个节点
        if (head == null) {
            return new AVLNode<>(key, value);
        } else {
            // 小的去左树
            if (key.compareTo(head.key) < 0) {
                head.left = add(head.left, key, value);
            } else {
                // 大的去右树
                head.right = add(head.right, key, value);
            }
            // 检查当前头的高度
            head.height = getHeight(head);
            // 检查4种违规
            return maintain(head);
        }
    }

    // 保证这个key在树上一定有
    // 删除key
    private AVLNode<K, V> delete(AVLNode<K, V> head, K key) {
        // 小了，往左树上删除并接住新头部
        if (key.compareTo(head.key) < 0) {
            head.left = delete(head.left, key);
        } else if (key.compareTo(head.key) > 0) {
            // 大了，往右树上删除并接住新头部
            head.right = delete(head.right, key);
        } else {
            // 删除head
            // head是叶子节点就直接置空
            if (head.left == null & head.right == null) {
                head = null;
            } else if (head.left != null & head.right == null) {
                // head只有左树就把左树拿上来
                head = head.left;
            } else if (head.left == null & head.right != null) {
                // head只有右树就把右树拿上来
                head = head.right;
            } else {
                // head左右子树都在
                // 我们找到head右树上的最左节点，也就是大于head且最接近head的数字
                AVLNode<K, V> rightMostLeft = head.right;
                while (rightMostLeft.left != null) {
                    rightMostLeft = rightMostLeft.left;
                }
                // 先在右树上把这个节点删除
                head.right = delete(head.right, rightMostLeft.key);
                // 再用这个节点替换head
                rightMostLeft.left = head.left;
                rightMostLeft.right = head.right;
                head = rightMostLeft;
            }
        }
        // 如果节点还在，修改一下节点的高度
        if (head != null) {
            head.height = getHeight(head);
        }
        // 检查违规
        return maintain(head);
    }

    // 如果树中有key，就找到它的父节点
    // 如果树中没key，就找到值离它最近的节点
    private AVLNode<K, V> findLastIndex(K key) {
        AVLNode<K, V> pre = root;
        AVLNode<K, V> cur = root;
        while (cur != null) {
            pre = cur;
            // cur的key就是要找的
            if (key.compareTo(cur.key) == 0) {
                break;
            } else if (key.compareTo(cur.key) < 0) {
                // key比cur的key小，在其左树上
                cur = cur.left;
            } else {
                // 在右树上
                cur = cur.right;
            }
        }
        return pre;
    }

    // 找到大于等于key的值中最近的那个节点
    private AVLNode<K, V> findLastNoSmallIndex(K key) {
        // 与上面的findLastIndex不同，如果树中没有这个节点就返回空
        AVLNode<K, V> ans = null;
        AVLNode<K, V> cur = root;
        while (cur != null) {
            if (key.compareTo(cur.key) == 0) {
                ans = cur;
                break;
            } else if (key.compareTo(cur.key) < 0) {
                // 当前key大于要找的，先记录，后面找到新的再更新
                ans = cur;
                cur = cur.left;
            } else {
                // 当前key小于要找的，直接跳过
                cur = cur.right;
            }
        }
        return ans;
    }

    // 与findLastNoSmallIndex方法一样，路径相反而已
    private AVLNode<K, V> findLastNoBigIndex(K key) {
        AVLNode<K, V> ans = null;
        AVLNode<K, V> cur = root;
        while (cur != null) {
            if (key.compareTo(cur.key) == 0) {
                ans = cur;
                break;
            } else if (key.compareTo(cur.key) > 0) {
                ans = cur;
                cur = cur.right;
            } else {
                cur = cur.left;
            }
        }
        return ans;
    }

    // 判断有序表中有没有这个节点
    public boolean containsKey(K key) {
        if (key == null) {
            return false;
        } else {
            AVLNode<K, V> lastNode = findLastIndex(key);
            // root可能为空
            return lastNode != null && key.compareTo(lastNode.key) == 0;
        }
    }

    // 把一对键值对添加到有序表中
    public void put(K key, V value) {
        if (key == null) {
            return;
        } else {
            // 赵丹最近的节点
            AVLNode<K, V> lastNode = findLastIndex(key);
            // 看看表中有没有这个节点
            // 有就直接更新
            // 没有就size+1然后去add这个节点
            if (lastNode != null && key.compareTo(lastNode.key) == 0) {
                lastNode.value = value;
            } else {
                size++;
                // 添加过程中树可能回变，root要接住新头节点
                root = add(root, key, value);
            }
        }
    }

    // 移除有序表中的key
    public void remove(K key) {
        if (key == null) {
            return;
        } else if (containsKey(key)) {
            // 首先要有这个key
            size--;
            root = delete(root, key);
        }
    }

    // 获取指定key的value
    public V get(K key) {
        if (key == null) {
            return null;
        } else {
            // 有就返回那个value不然就返回空
            AVLNode<K, V> lastNode = findLastIndex(key);
            return lastNode != null && key.compareTo(lastNode.key) == 0 ? lastNode.value : null;
        }
    }

    // 找到有序表中最小的key
    public K firstKey() {
        if (root == null) {
            return null;
        } else {
            AVLNode<K, V> cur = root;
            while (cur.left != null) {
                cur = cur.left;
            }
            return cur.key;
        }
    }

    // 找到有序表中最大的key
    public K lastKey() {
        if (root == null) {
            return null;
        } else {
            AVLNode<K, V> cur = root;
            while (cur.right != null) {
                cur = cur.right;
            }
            return cur.key;
        }
    }

    // 返回小于或等于给定键的最大键，如果没有这样的键，则 null
    public K floorKey(K key) {
        if (key == null) {
            return null;
        } else {
            AVLNode<K, V> lastNoBigNode = findLastNoBigIndex(key);
            return lastNoBigNode == null ? null : lastNoBigNode.key;
        }
    }

    // 返回大于或等于给定键的最小键，如果没有这样的键，则 null
    public K ceilingKey(K key) {
        if (key == null) {
            return null;
        } else {
            AVLNode<K, V> lastNoSmallNode = findLastNoSmallIndex(key);
            return lastNoSmallNode == null ? null : lastNoSmallNode.key;
        }
    }
}
```
