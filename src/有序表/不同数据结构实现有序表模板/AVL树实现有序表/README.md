# AVL树实现有序表

## [1382. 将二叉搜索树变平衡](https://leetcode.cn/problems/balance-a-binary-search-tree/)

> - **Question 1**
>   - 给你一棵二叉搜索树，请你返回一棵平衡后的二叉搜索树，新生成的树应该与原来的树有着相同的节点值。如果有多种构造方法，请你返回任意一种。
>   - 如果一棵二叉搜索树中，每个节点的两棵子树高度差不超过 `1` ，我们就称这棵二叉搜索树是平衡的。
>   - **Tips**
>     - 树节点的数目在 `[1, 10^4]` 范围内
>     - `1 <= Node.val <= 10^5`

## [P3369 【模板】普通平衡树](https://www.luogu.com.cn/problem/P3369)

> - **Question 2**
>   - 您需要写一种数据结构（可参考题目标题），来维护一些数，其中需要提供以下操作：
>     1. 插入一个数 `x` 。
>     2. 删除一个数 `x` （若有多个相同的数，应只删除一个）。
>     3. 定义排名为比当前数小的数的个数 `+ 1` 。查询 `x` 的排名。
>     4. 查询数据结构中排名为 `x` 的数。
>     5. 求 `x` 的前驱（前驱定义为小于 `x` ，且最大的数）。
>     6. 求 `x` 的后继（后继定义为大于 `x` ，且最小的数）。
>   - 对于操作 `3, 5, 6` ，不保证当前数据结构中存在数 `x` 。
>   - **输入描述**
>     - 第一行为 `n` ，表示操作的个数,下面 `n` 行每行有两个数 `opt` 和 `x` ， `opt` 表示操作的序号（ `1 <= opt <= 6` ）。
>   - **输出描述**
>     - 对于操作 `3, 4, 5, 6` 每行输出一个数，表示对应答案。
>   - **Tips**
>     - `1 <=n <= 10^5`
>     - `|x| <= 10^7`

## Java

> - **Question 1: 节点类实现AVL树**
>   - 首先这是一颗BST，对于一个节点来说，它的左子树上的所有值都小于它，右子树上的都大于它（在有序表中我们认为这个值是 `key` ），且没有重复值。
>   - 其次，AVL树的平衡条件是对于任何一个节点来说左右子树之间的高度差不能大于1，为此有四种违规类型和对应的处理方式：
>     - `LL` ，左子树高度高于右子树的高度，且左子树的左子树较高，对头节点右旋。
>     - `LR` ，左子树高度高于右子树的高度，且左子树的右子树较高，先对左子树左旋，再对头节点右旋。
>     - `RR` ，左子树高度低于右子树的高度，且右子树的右子树较高，对头节点左旋。
>     - `RL` ，左子树高度低于右子树的高度，且右子树的左子树较高，先对左子树左旋，再对头节点右旋。
>     - **LL和LR型同时出现时，用LL型，RR和RL型同理。**
>     - 旋转不会改变树的有序性，且能够让树变得平一些。
>     - 一个节点右旋是指它向右倒下，它左孩子替代它的位置，它左孩子的右孩子挂到它左孩子下，它成为它左孩子的右孩子，其他同理。
>   - AVL树本质上就是BST打上了平衡性的补丁，实现有序表时用的还是搜索二叉树的方式，只是在增删等过程中加入了平衡性的检查与调整。

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

> - **贪心 + 中序遍历 + 二分使树变平衡**
>   - 贪心策略：我们可以选择树的中序遍历序列的中间位置作为二叉搜索树的根节点，这样分给左右子树的数字个数相同或只相差 `1` ，可以使得树保持平衡。
>   - 先获取中序序列，然后取中点或者上中节点作为根节点，递归构建左树和右树即可。
>   - 获得中序遍历的时间代价是 `O(n)` ，建立平衡二叉树的时建立每个点的时间代价为 `O(1)` ，总时间也是 `O(n)` 。故渐进时间复杂度为 `O(n)` 。这里使用了一个数组作为辅助空间，存放中序遍历后的有序序列，故渐进空间复杂度为 `O(n)` 。
> - **暴力旋转**
>   - 注意节点类型不同，对高度的处理不同。
>   - 注意要像SB树一样递归看子孩子平衡否？

```java
class TreeNode {
    
    int val;
    TreeNode left;
    TreeNode right;
    
    TreeNode() {}
    
    TreeNode(int val) {this.val = val;}
    
    TreeNode(int val, TreeNode left, TreeNode right) {
        this.val = val;
        this.left = left;
        this.right = right;
    }
    
}


class Solution {
    
    public TreeNode balanceBST(TreeNode root) {
        if (root == null) {
            return null;
        }
        
        // return balanceBSTByRotate(root);
        return balanceBSTByInorder(root);
    }
    
    // 中序遍历序列
    private final ArrayList<TreeNode> inorderList = new ArrayList<>();
    
    // 贪心
    private TreeNode balanceBSTByInorder(TreeNode root) {
        getInorder(root);
        return inorder(0, inorderList.size() - 1);
    }
    
    // 获取中序遍历序列
    private void getInorder(TreeNode head) {
        if (head.left != null) {
            getInorder(head.left);
        }
        inorderList.add(head);
        if (head.right != null) {
            getInorder(head.right);
        }
    }
    
    // 重新建树
    private TreeNode inorder(int left, int right) {
        int mid = (left + right) / 2;
        TreeNode head = inorderList.get(mid);
        // 注意，如果来到了叶子节点，要把之前树中的指向断开并设为空
        head.left = left <= mid - 1 ? inorder(left, mid - 1) : null;
        head.right = mid + 1 <= right ? inorder(mid + 1, right) : null;
        return head;
    }
    
    // 高度缓存表
    private final HashMap<TreeNode, Integer> height = new HashMap<>();
    
    // 旋转
    private TreeNode balanceBSTByRotate(TreeNode root) {
        getHeight(root);
        return changeTree(root);
    }
    
    // 自底向上调整平衡
    private TreeNode changeTree(TreeNode head) {
        if (head == null) {
            return null;
        }
        head.left = changeTree(head.left);
        head.right = changeTree(head.right);
        changeHeight(head);
        return keepBalance(head);
    }
    
    // 获取一个节点的高度
    // 整棵树各个节点的高度加入缓存表，题目要求的代码中没有高度属性
    private int getHeight(TreeNode head) {
        if (head == null) {
            return 0;
        }
        int leftHeight = getHeight(head.left);
        int rightHeight = getHeight(head.right);
        int headHeight = Math.max(leftHeight, rightHeight) + 1;
        height.put(head, headHeight);
        return headHeight;
    }
    
    // 改变一个节点的高度
    private void changeHeight(TreeNode head) {
        height.put(head, 1 + Math.max(head.left != null ? height.get(head.left) : 0, head.right != null ? height.get(head.right) : 0));
    }
    
    private TreeNode leftRotate(TreeNode head) {
        TreeNode right = head.right;
        head.right = right.left;
        right.left = head;
        changeHeight(head);
        changeHeight(right);
        return right;
    }
    
    private TreeNode rightRotate(TreeNode head) {
        TreeNode left = head.left;
        head.left = left.right;
        left.right = head;
        changeHeight(head);
        changeHeight(left);
        return left;
    }
    
    private TreeNode keepBalance(TreeNode head) {
        if (head == null) {
            return null;
        }
        int leftHeight = head.left != null ? height.get(head.left) : 0;
        int rightHeight = head.right != null ? height.get(head.right) : 0;
        int leftLeftHeight = head.left != null && head.left.left != null ? height.get(head.left.left) : 0;
        int leftRightHeight = head.left != null && head.left.right != null ? height.get(head.left.right) : 0;
        int rightRightHeight = head.right != null && head.right.right != null ? height.get(head.right.right) : 0;
        int rightLeftHeight = head.right != null && head.right.left != null ? height.get(head.right.left) : 0;
        if (Math.abs(leftHeight - rightHeight) > 1) {
            if (leftHeight > rightHeight) {
                if (leftLeftHeight >= leftRightHeight) {
                    head = rightRotate(head);
                } else {
                    head.left = leftRotate(head.left);
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
        // 和代码模板不一样，因为add只会影响插入位置开始上面的树的平衡，它下面的位置是平衡的
        // 如果直接自底向上调整平衡，每次调完一个节点要像sb树一样递归看孩子有没有平衡
        head.left = keepBalance(head.left);
        head.right = keepBalance(head.right);
        changeHeight(head);
        return head;
    }
    
}
```

> - **Question 2: AVL树改有序表新版本**

```java
import java.io.*;
import java.util.Arrays;

public class Main {

    public static int MAXN = 100001;

    // 空间使用计数
    public static int cnt = 0;

    // 整棵树的头节点编号
    public static int head = 0;

    // 节点的key
    public static int[] key = new int[MAXN];

    // 子树高度
    public static int[] height = new int[MAXN];

    // 左孩子
    public static int[] left = new int[MAXN];

    // 右孩子
    public static int[] right = new int[MAXN];

    // 节点key的计数
    public static int[] count = new int[MAXN];

    // 子树的数字总数
    public static int[] size = new int[MAXN];

    // 修正信息
    public static void up(int i) {
        size[i] = size[left[i]] + size[right[i]] + count[i];
        height[i] = Math.max(height[left[i]], height[right[i]]) + 1;
    }

    // i节点为头的树左旋，返回左旋后头节点的空间编号
    public static int leftRotate(int i) {
        int r = right[i];
        right[i] = left[r];
        left[r] = i;
        up(i);
        up(r);
        return r;
    }

    // i节点为头的树右旋，返回右旋后头节点的空间编号
    public static int rightRotate(int i) {
        int l = left[i];
        left[i] = right[l];
        right[l] = i;
        up(i);
        up(l);
        return l;
    }

    // 检查i节点为头的树是否违规
    // 如果命中了某种违规情况，就进行相应调整
    // 返回树的头节点的空间编号
    public static int maintain(int i) {
        int lh = height[left[i]];
        int rh = height[right[i]];
        if (lh - rh > 1) {
            if (height[left[left[i]]] >= height[right[left[i]]]) {
                i = rightRotate(i);
            } else {
                left[i] = leftRotate(left[i]);
                i = rightRotate(i);
            }
        } else if (rh - lh > 1) {
            if (height[right[right[i]]] >= height[left[right[i]]]) {
                i = leftRotate(i);
            } else {
                right[i] = rightRotate(right[i]);
                i = leftRotate(i);
            }
        }
        return i;
    }

    // 增加数字num，重复加入算多个词频
    public static void add(int num) {
        head = add(head, num);
    }

    // 当前来到i号节点，num这个数字一定会加入以i为头的树
    // 树结构有可能变化，返回头节点编号
    public static int add(int i, int num) {
        if (i == 0) {
            key[++cnt] = num;
            count[cnt] = size[cnt] = height[cnt] = 1;
            return cnt;
        }
        if (key[i] == num) {
            count[i]++;
        } else if (key[i] > num) {
            left[i] = add(left[i], num);
        } else {
            right[i] = add(right[i], num);
        }
        up(i);
        return maintain(i);
    }

    // 删除数字num，如果有多个，只删掉一个
    public static void remove(int num) {
        if (rank(num) != rank(num + 1)) {
            head = remove(head, num);
        }
    }

    // 当前来到i号节点，以i为头的树一定会减少1个num
    // 树结构有可能变化，返回头节点编号
    public static int remove(int i, int num) {
        if (key[i] < num) {
            right[i] = remove(right[i], num);
        } else if (key[i] > num) {
            left[i] = remove(left[i], num);
        } else {
            if (count[i] > 1) {
                count[i]--;
            } else {
                if (left[i] == 0 && right[i] == 0) {
                    return 0;
                } else if (left[i] != 0 && right[i] == 0) {
                    i = left[i];
                } else if (left[i] == 0 && right[i] != 0) {
                    i = right[i];
                } else {
                    int mostLeft = right[i];
                    while (left[mostLeft] != 0) {
                        mostLeft = left[mostLeft];
                    }
                    right[i] = removeMostLeft(right[i], mostLeft);
                    left[mostLeft] = left[i];
                    right[mostLeft] = right[i];
                    i = mostLeft;
                }
            }
        }
        up(i);
        return maintain(i);
    }

    // 以i号节点为头的树上，最左节点的编号一定是mostLeft
    // 删掉这个节点，并保证树的平衡性，返回头节点的编号
    public static int removeMostLeft(int i, int mostLeft) {
        if (i == mostLeft) {
            return right[i];
        } else {
            left[i] = removeMostLeft(left[i], mostLeft);
            up(i);
            return maintain(i);
        }
    }

    // 查询num的排名，比num小的数字个数+1，就是num的排名
    public static int rank(int num) {
        return small(head, num) + 1;
    }

    // 以i为头的树上，比num小的数字有几个
    public static int small(int i, int num) {
        if (i == 0) {
            return 0;
        }
        if (key[i] >= num) {
            return small(left[i], num);
        } else {
            return size[left[i]] + count[i] + small(right[i], num);
        }
    }

    public static int index(int x) {
        return index(head, x);
    }

    public static int index(int i, int x) {
        if (size[left[i]] >= x) {
            return index(left[i], x);
        } else if (size[left[i]] + count[i] < x) {
            return index(right[i], x - size[left[i]] - count[i]);
        }
        return key[i];
    }

    public static int pre(int num) {
        return pre(head, num);
    }

    public static int pre(int i, int num) {
        if (i == 0) {
            return Integer.MIN_VALUE;
        }
        if (key[i] >= num) {
            return pre(left[i], num);
        } else {
            return Math.max(key[i], pre(right[i], num));
        }
    }

    public static int post(int num) {
        return post(head, num);
    }

    public static int post(int i, int num) {
        if (i == 0) {
            return Integer.MAX_VALUE;
        }
        if (key[i] <= num) {
            return post(right[i], num);
        } else {
            return Math.min(key[i], post(left[i], num));
        }
    }

    public static void clear() {
        Arrays.fill(key, 1, cnt + 1, 0);
        Arrays.fill(height, 1, cnt + 1, 0);
        Arrays.fill(left, 1, cnt + 1, 0);
        Arrays.fill(right, 1, cnt + 1, 0);
        Arrays.fill(count, 1, cnt + 1, 0);
        Arrays.fill(size, 1, cnt + 1, 0);
        cnt = 0;
        head = 0;
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        int n = (int) in.nval;
        for (int i = 1, op, x; i <= n; i++) {
            in.nextToken();
            op = (int) in.nval;
            in.nextToken();
            x = (int) in.nval;
            if (op == 1) {
                add(x);
            } else if (op == 2) {
                remove(x);
            } else if (op == 3) {
                out.println(rank(x));
            } else if (op == 4) {
                out.println(index(x));
            } else if (op == 5) {
                out.println(pre(x));
            } else {
                out.println(post(x));
            }
        }
        clear();
        out.flush();
        out.close();
        br.close();
    }

}
```
