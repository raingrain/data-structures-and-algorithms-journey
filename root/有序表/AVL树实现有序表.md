# AVL树实现有序表

---

## *Java*

> - ***节点类实现AVL树***
>   - 首先这是一颗BST，对于一个节点来说，它的左子树上的所有值都小于它，右子树上的都大于它（在有序表中我们认为这个值是 `key` ），且没有重复值
>   - 其次，AVL树的平衡条件是对于任何一个节点来说左右子树之间的高度差不能大于1，为此有四种违规类型和对应的处理方式：
>     - `LL` ，左子树高度高于右子树的高度，且左子树的左子树较高，对头节点右旋
>     - `LR` ，左子树高度高于右子树的高度，且左子树的右子树较高，先对左子树左旋，再对头节点右旋
>     - `RR` ，左子树高度低于右子树的高度，且右子树的右子树较高，对头节点左旋
>     - `RL` ，左子树高度低于右子树的高度，且右子树的左子树较高，先对左子树左旋，再对头节点右旋
>     - **LL和LR型同时出现时，用LL型，RR和RL型同理**
>     - 旋转不会改变树的有序性，且能够让树变得平一些
>     - 一个节点右旋是指它向右倒下，它左孩子替代它的位置，它左孩子的右孩子挂到它左孩子下，它成为它左孩子的右孩子，其他同理
>   - AVL树本质上就是BST打上了平衡性的补丁，实现有序表时用的还是搜索二叉树的方式，只是在增删等过程中加入了平衡性的检查与调整

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
    
    // 返回有序表中的最小key
    public K minimumKey();
    
    // 返回有序表中的最大key
    public K maximumKey();
    
    // 返回有序表中小于或等于给定键的最大键，如果没有这样的键，则 null
    public K floorKey(K key);
    
    // 返回有序表中大于或等于给定键的最小键，如果没有这样的键，则 null
    public K ceilingKey(K key);
    
}

// AVL树的节点
class AVLNode<K extends Comparable<K>, V> {
    
    // 键值对
    public K key;
    public V value;
    // 平衡因子：以该节点为头的树的高度
    public int height;
    public AVLNode<K, V> left;
    public AVLNode<K, V> right;
    
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
    
    // 获取一个节点为头时整棵树的高度
    private int getHeight(AVLNode<K, V> head) {
        return 1 + Math.max(head.left != null ? head.left.height : 0, head.right != null ? head.right.height : 0);
    }
    
    // 左旋
    private AVLNode<K, V> leftRotate(AVLNode<K, V> head) {
        AVLNode<K, V> right = head.right;
        head.right = right.left;
        right.left = head;
        head.height = getHeight(head);
        right.height = getHeight(right);
        return right;
    }
    
    // 右旋
    private AVLNode<K, V> rightRotate(AVLNode<K, V> head) {
        // 抓住要升上来的节点
        AVLNode<K, V> left = head.left;
        // head的左树变成要上升的节点的右树
        head.left = left.right;
        // 将head挂载到上升节点的右树上，实现了旋转
        left.right = head;
        // 在这次旋转中，由于head的左树和left的右树发生了变化，所以要检查它们的高度，它门各自的另外一颗子树并没有发生变化，不用检查
        head.height = getHeight(head);
        left.height = getHeight(left);
        // 把新局部树的根节点返回
        return left;
    }
    
    // 违规性的检查
    private AVLNode<K, V> keepBalance(AVLNode<K, V> head) {
        if (head == null) {
            return null;
        }
        int leftHeight = head.left != null ? head.left.height : 0;
        int rightHeight = head.right != null ? head.right.height : 0;
        int leftLeftHeight = head.left != null && head.left.left != null ? head.left.left.height : 0;
        int leftRightHeight = head.left != null && head.left.right != null ? head.left.right.height : 0;
        int rightRightHeight = head.right != null && head.right.right != null ? head.right.right.height : 0;
        int rightLeftHeight = head.right != null && head.right.left != null ? head.right.left.height : 0;
        // 左右树高度差大于等于2就是违规
        if (Math.abs(leftHeight - rightHeight) > 1) {
            if (leftHeight > rightHeight) {
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
    
    // 不会撞到相同的key，相同的key直接更新，只会增加key
    private AVLNode<K, V> add(AVLNode<K, V> head, K key, V value) {
        if (head == null) {
            return new AVLNode<>(key, value);
        }
        if (key.compareTo(head.key) < 0) {
            // 小的去左树
            head.left = add(head.left, key, value);
        } else {
            // 大的去右树
            head.right = add(head.right, key, value);
        }
        // 检查当前头的高度
        head.height = getHeight(head);
        // 添加后自加入处往上检查4种违规
        return keepBalance(head);
    }
    
    // 保证这个key在树上一定有
    // 删除key
    private AVLNode<K, V> delete(AVLNode<K, V> head, K key) {
        if (key.compareTo(head.key) < 0) {
            // 小了，往左树上删除并接住新头部
            head.left = delete(head.left, key);
        } else if (key.compareTo(head.key) > 0) {
            // 大了，往右树上删除并接住新头部
            head.right = delete(head.right, key);
        } else {
            // 删除head
            if (head.left == null & head.right == null) {
                // head是叶子节点就直接置空
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
                AVLNode<K, V> rightTreeMostLeft = head.right;
                while (rightTreeMostLeft.left != null) {
                    rightTreeMostLeft = rightTreeMostLeft.left;
                }
                // 先在右树上把这个节点删除
                head.right = delete(head.right, rightTreeMostLeft.key);
                // 再用这个节点替换head
                rightTreeMostLeft.left = head.left;
                rightTreeMostLeft.right = head.right;
                head = rightTreeMostLeft;
            }
        }
        // 如果节点还在，修改一下节点的高度
        if (head != null) {
            head.height = getHeight(head);
        }
        // 由于我们在上面直接在右子树中删除了右数上的最左节点，右子树自身的违规已经检察过，我们从head开始检查违规
        return keepBalance(head);
    }
    
    // 在AVL树中找到key值距离给定key最接近的节点，可以相等
    private AVLNode<K, V> findNearestNode(K key) {
        AVLNode<K, V> pre = root;
        AVLNode<K, V> cur = root;
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
    
    // 在AVL树中找到key值距离给定key最接近且大于等于给定key的节点，可以相等
    private AVLNode<K, V> findNearestAndNoSmallNode(K key) {
        AVLNode<K, V> ans = null;
        AVLNode<K, V> cur = root;
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
    
    // 在AVLb树中找到key值距离给定key最接近且小于等于给定key的节点，可以相等
    private AVLNode<K, V> findNearestAndNoBigNode(K key) {
        AVLNode<K, V> ans = null;
        AVLNode<K, V> cur = root;
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
            throw new RuntimeException("invalid parameter.");
        }
        AVLNode<K, V> nearestNode = findNearestNode(key);
        return nearestNode != null && key.compareTo(nearestNode.key) == 0;
    }
    
    @Override
    public void put(K key, V value) {
        if (key == null) {
            throw new RuntimeException("invalid parameter.");
        }
        AVLNode<K, V> nearestNode = findNearestNode(key);
        // 表中有这个节点就直接更新，没有size就+1然后调用add
        if (nearestNode != null && key.compareTo(nearestNode.key) == 0) {
            nearestNode.value = value;
        } else {
            size++;
            root = add(root, key, value);
        }
    }
    
    @Override
    public void remove(K key) {
        if (key == null) {
            throw new RuntimeException("invalid parameter.");
        }
        // 确保有这个key
        if (containsKey(key)) {
            root = delete(root, key);
        }
    }
    
    @Override
    public V get(K key) {
        if (key == null) {
            throw new RuntimeException("invalid parameter.");
        }
        AVLNode<K, V> nearestNode = findNearestNode(key);
        // 有就返回value不然返回空
        return nearestNode != null && key.compareTo(nearestNode.key) == 0 ? nearestNode.value : null;
    }
    
    @Override
    public K minimumKey() {
        if (root == null) {
            return null;
        }
        AVLNode<K, V> cur = root;
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
        AVLNode<K, V> cur = root;
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
        AVLNode<K, V> nearestAndNoBigNode = findNearestAndNoBigNode(key);
        return nearestAndNoBigNode == null ? null : nearestAndNoBigNode.key;
    }
    
    @Override
    public K ceilingKey(K key) {
        if (key == null) {
            throw new RuntimeException("invalid parameter.");
        }
        AVLNode<K, V> nearestAndNoSmallNode = findNearestAndNoSmallNode(key);
        return nearestAndNoSmallNode == null ? null : nearestAndNoSmallNode.key;
    }
    
    public AVLNode<K, V> getRoot() {
        return root;
    }
    
}
```

---

> ***last change: 2022/10/21***

---
