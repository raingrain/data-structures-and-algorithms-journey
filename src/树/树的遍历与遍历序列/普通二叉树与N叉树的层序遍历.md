# 普通二叉树与N叉树的层序遍历

## [剑指 Offer 32 - I. 从上到下打印二叉树](https://leetcode.cn/problems/cong-shang-dao-xia-da-yin-er-cha-shu-lcof/)

> - ***Question 1***
>   - 实现二叉树的层序遍历，返回一个存储全部节点的数组。

## [102. 二叉树的层序遍历](https://leetcode.cn/problems/binary-tree-level-order-traversal/)

> - ***Question 2***
>   - 实现二叉树的层序遍历，每一层的节点用一个链表来存储，返回由每层链表的头节点组成的链表（二维链表）。

## [剑指 Offer 32 - II. 从上到下打印二叉树 II](https://leetcode.cn/problems/cong-shang-dao-xia-da-yin-er-cha-shu-ii-lcof/)

## [107. 二叉树的层序遍历 II](https://leetcode.cn/problems/binary-tree-level-order-traversal-ii/)

> - ***Question 3***
>   - 实现二叉树的逆层序遍历（即按从叶子节点所在层到根节点所在的层，逐层从左向右遍历），返回和 `Question 2` 相同的结构。

## [103. 二叉树的锯齿形层序遍历](https://leetcode.cn/problems/binary-tree-zigzag-level-order-traversal/)

## [剑指 Offer 32 - III. 从上到下打印二叉树 III](https://leetcode.cn/problems/cong-shang-dao-xia-da-yin-er-cha-shu-iii-lcof/)

> - ***Question 4***
>   - 实现二叉树的锯齿形层序遍历（之字形顺序）（即先从左往右（根节点所在层），再从右往左进行下一层遍历，以此类推，层与层之间交替进行），返回和***Question 2***相同的结构。

## [429. N 叉树的层序遍历](https://leetcode.cn/problems/n-ary-tree-level-order-traversal/)

> - ***Question 5***
>   - 给定一个 `N` 叉树，返回其节点值的层序遍历。

## [116. 填充每个节点的下一个右侧节点指针](https://leetcode.cn/problems/populating-next-right-pointers-in-each-node/)

## [117. 填充每个节点的下一个右侧节点指针 II](https://leetcode.cn/problems/populating-next-right-pointers-in-each-node-ii/)

> - ***Question 6***
>   - 给定一个二叉树，结构如代码中所示，填充它的每个 `next` 指针，让这个指针指向其下一个右侧节点（层序遍历中的下一个节点）。如果找不到下一个右侧节点（层序遍历中每一层的最后一个节点），则将 `next` 指针设置为 `null` 。
>   - 初始状态下，所有 `next` 指针都被设置为 `null` 。
>   - ***tips:***
>     - 树中的节点数在范围 `[0, 6000]` 内
>     - `-100 <= Node.val <= 100`
>     - 你只能使用常量级额外空间

## [199. 二叉树的右视图](https://leetcode.cn/problems/binary-tree-right-side-view/)

## [LCR 046. 二叉树的右视图](https://leetcode.cn/problems/WNC0Lk/)

> - ***Question 7***
>   - 给定一个二叉树的根节点 `root` ，想象自己站在它的右侧，按照从顶部到底部的顺序，返回从右侧所能看到的节点值。
>   - ***tips:***
>     - `二叉树的节点个数的范围是 [0, 100]`
>     - `-100 <= Node.val <= 100`

## [515. 在每个树行中找最大值](https://leetcode.cn/problems/find-largest-value-in-each-tree-row/)

## [LCR 044. 在每个树行中找最大值](https://leetcode.cn/problems/hPov7L/)

> - ***Question 8***
>   - 给定一棵二叉树的根节点 `root` ，请找出该二叉树中每一层的最大值。
>   - ***tips:***
>     - 二叉树的节点个数的范围是 `[0, 10^4]`
>     - `-2^31 <= Node.val <= 2^31 - 1`

## [513. 找树左下角的值](https://leetcode.cn/problems/find-bottom-left-tree-value/)

## [LCR 045. 找树左下角的值](https://leetcode.cn/problems/LwUNpT/)

> - ***Question 9***
>   - 给定一个二叉树的根节点 `root` ，请找出该二叉树的最底层最左边 节点的值。
>   - 假设二叉树中至少有一个节点。
>   - ***tips:***
>     - 二叉树的节点个数的范围是 `[1, 10^4]`
>     - `-2^31 <= Node.val <= 2^31 - 1`

## [面试题 04.03. 特定深度节点链表](https://leetcode.cn/problems/list-of-depth-lcci/)

> - ***Question 10***
>   - 给定一棵二叉树，设计一个算法，创建含有某一深度上所有节点的链表（比如，若一棵树的深度为 `D` ，则会创建出 `D` 个链表）。返回一个包含所有深度的链表的数组。

## [637. 二叉树的层平均值](https://leetcode.cn/problems/average-of-levels-in-binary-tree/)

> - ***Question 11***
>   - 给定一个非空二叉树的根节点 `root` , 以数组的形式返回每一层节点的平均值。与实际答案相差 `10^-5` 以内的答案可以被接受。
>   - ***tips:***
>     - 树中节点数量在 `[1, 10^4]` 范围内
>     - `-2^31 <= Node.val <= 2^31 - 1`

---

## *Java*

> - ***广度优先搜索 BFS***
>   - 准备一个队列以及存储结果的容器，初始时头入队，进循环：
>     - 如果是结果不区分层，则直接将队列中元素元素出队，然后加到结果中，并把非空的左右孩子入队。
>     - 如果结果分层，则新建一个链表来存储结果，获取队列的长度，用一个 `for` 循环依次弹出这一层的元素，弹出后加入用于存储当前层的链表中（锯齿形层序遍历在这一步根据插入方向选择头插和尾插即可），并把非空左右孩子入队， `for` 循环结束后把存储当前层的元素的链表加入到头节点中（逆层序遍历在这一步改成头插即可）。
>   - `N` 叉树的操作除了入队时要遍历子节点链表外，其他和二叉树一样。
>   - 时间复杂度与空间复杂度都是 `O(n)` ，`n` 为二叉树节点个数，因为每个节点只被访问一次，队列以及结果容器的节点数不超过 `n` 。

```java
// N叉树节点
class Node {
    
    public int val;
    public List<Node> children;
    
    public Node() {}
    
    public Node(int _val) {
        val = _val;
    }
    
    public Node(int _val, List<Node> _children) {
        val = _val;
        children = _children;
    }
    
}

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

// Question 10
class ListNode {

    int val;
    ListNode next;

    ListNode(int x) {
        val = x;
    }

}

class Solution {
    
    // 层序遍历存储在数组中
    public int[] levelOrderToArray(TreeNode root) {
        if (root == null) {
            return new int[0];
        }
        ArrayList<Integer> ansLinkedList = new ArrayList<>();
        Queue<TreeNode> queue = new ArrayDeque<>();
        queue.add(root);
        while (!queue.isEmpty()) {
            root = queue.poll();
            ansLinkedList.add(root.val);
            if (root.left != null) {
                queue.add(root.left);
            }
            if (root.right != null) {
                queue.add(root.right);
            }
        }
        int[] ansArray = new int[ansLinkedList.size()];
        for (int i = 0; i < ansArray.length; ++i) {
            ansArray[i] = ansLinkedList.get(i);
        }
        return ansArray;
    }
    
    // 层序遍历存储在二维链表中
    public List<List<Integer>> levelOrder(TreeNode root) {
        if (root == null) {
            return new ArrayList<>();
        }
        List<List<Integer>> ans = new LinkedList<>();
        Deque<TreeNode> deque = new ArrayDeque<>();
        deque.add(root);
        while (!deque.isEmpty()) {
            List<Integer> curLevel = new LinkedList<>();
            for (int i = deque.size(); i > 0; --i) {
                TreeNode node = deque.poll();
                assert node != null;
                curLevel.add(node.val);
                if (node.left != null) {
                    deque.add(node.left);
                }
                if (node.right != null) {
                    deque.add(node.right);
                }
            }
            ans.add(curLevel);
        }
        return ans;
    }

    // Question 7即层序遍历按层遍历中队列每层最后一个节点
    public List<Integer> rightSideView(TreeNode root) {
        if (root == null) {
            return new ArrayList<>();
        }
        ArrayDeque<TreeNode> deque = new ArrayDeque<>();
        List<Integer> ans = new ArrayList<>();
        deque.add(root);
        while (!deque.isEmpty()) {
            for (int i = deque.size(); i > 0; --i) {
                TreeNode node = deque.poll();
                if (node.left != null) {
                    deque.add(node.left);
                }
                if (node.right != null) {
                    deque.add(node.right);
                }
                if (i == 1) {
                    ans.add(node.val);
                }
            }
        }
        return ans;
    }

    // Question 8
    public List<Integer> largestValues(TreeNode root) {
        if (root == null) {
            return new ArrayList<>();
        }
        ArrayDeque<TreeNode> deque = new ArrayDeque<>();
        List<Integer> ans = new ArrayList<>();
        deque.add(root);
        while (!deque.isEmpty()) {
            int max = Integer.MIN_VALUE;
            for (int i = deque.size(); i > 0; --i) {
                TreeNode node = deque.poll();
                // 每层记录最大值
                max = Math.max(max, node.val);
                if (node.left != null) {
                    deque.add(node.left);
                }
                if (node.right != null) {
                    deque.add(node.right);
                }
            }
            ans.add(max);
        }
        return ans;
    }

    // Question 9
    public int findBottomLeftValue(TreeNode root) {
        ArrayDeque<TreeNode> deque = new ArrayDeque<>();
        int ans = 0;
        deque.add(root);
        while (!deque.isEmpty()) {
            // 每层记录第一个
            ans = deque.peek().val;
            for (int i = deque.size(); i > 0; --i) {
                TreeNode node = deque.poll();
                if (node.left != null) {
                    deque.add(node.left);
                }
                if (node.right != null) {
                    deque.add(node.right);
                }
            }
        }
        return ans;
    }

    // Question 10
    public ListNode[] listOfDepth(TreeNode root) {
        if (root == null) {
            return new ListNode[]{};
        }
        ArrayList<ListNode> ansList = new ArrayList<>();
        ArrayDeque<TreeNode> queue = new ArrayDeque<>();
        queue.add(root);
        while (!queue.isEmpty()) {
            ListNode guardRoot = new ListNode(0);
            ListNode cur = guardRoot;
            for (int i = queue.size(); i > 0; --i) {
                TreeNode node = queue.poll();
                assert node != null;
                cur.next = new ListNode(node.val);
                cur = cur.next;
                if (node.left != null) {
                    queue.add(node.left);
                }
                if (node.right != null) {
                    queue.add(node.right);
                }
            }
            ansList.add(guardRoot.next);
        }
        ListNode[] ans = new ListNode[ansList.size()];
        for (int i = 0; i < ansList.size(); i++) {
            ans[i] = ansList.get(i);
        }
        return ans;
    }

    // Question 11
    public List<Double> averageOfLevels(TreeNode root) {
        if (root == null) {
            return new ArrayList<>();
        }
        ArrayDeque<TreeNode> deque = new ArrayDeque<>();
        List<Double> ans = new ArrayList<>();
        deque.add(root);
        while (!deque.isEmpty()) {
            int len = deque.size();
            double sum = 0;
            for (int i = deque.size(); i > 0; --i) {
                TreeNode node = deque.poll();
                sum += node.val;
                if (node.left != null) {
                    deque.add(node.left);
                }
                if (node.right != null) {
                    deque.add(node.right);
                }
            }
            ans.add(sum / len);
        }
        return ans;
    }
    
    // 逆层序遍历存储在二维链表中
    public List<List<Integer>> levelOrderBottom(TreeNode root) {
        if (root == null) {
            return new ArrayList<>();
        }
        List<List<Integer>> ans = new LinkedList<>();
        Deque<TreeNode> deque = new ArrayDeque<>();
        deque.add(root);
        while (!deque.isEmpty()) {
            List<Integer> curLevel = new LinkedList<>();
            for (int i = deque.size(); i > 0; --i) {
                TreeNode node = deque.poll();
                assert node != null;
                curLevel.add(node.val);
                if (node.left != null) {
                    deque.add(node.left);
                }
                if (node.right != null) {
                    deque.add(node.right);
                }
            }
            // 尾插
            ans.add(0, curLevel);
        }
        return ans;
    }
    
    // 锯齿形层序遍历存储在二维链表中
    public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        if (root == null) {
            return new ArrayList<>();
        }
        List<List<Integer>> ans = new LinkedList<>();
        Deque<TreeNode> deque = new ArrayDeque<>();
        // 插入方向是否从左往右
        // 根节点所在层是的
        boolean isFromLeftToRight = true;
        deque.add(root);
        while (!deque.isEmpty()) {
            List<Integer> curLevel = new LinkedList<>();
            for (int i = deque.size(); i > 0; --i) {
                TreeNode node = deque.poll();
                assert node != null;
                // 根据插入方向选择头插还是尾插
                if (isFromLeftToRight) {
                    curLevel.add(node.val);
                } else {
                    curLevel.add(0, node.val);
                }
                if (node.left != null) {
                    deque.add(node.left);
                }
                if (node.right != null) {
                    deque.add(node.right);
                }
            }
            isFromLeftToRight = !isFromLeftToRight;
            ans.add(curLevel);
        }
        return ans;
    }

    // N叉树的层序遍历
    public List<List<Integer>> levelOrder(Node root) {
        return levelOrderByIteration(root);
    }
    
    // 层序遍历存储在二维链表中
    public List<List<Integer>> levelOrderByIteration(Node root) {
        List<List<Integer>> ans = new LinkedList<>();
        if (root == null) {
            return ans;
        }
        Queue<Node> queue = new ArrayDeque<>();
        queue.offer(root);
        while (!queue.isEmpty()) {
            List<Integer> cueLevel = new LinkedList<>();
            for (int i = queue.size(); i > 0; --i) {
                root = queue.poll();
                cueLevel.add(root.val);
                // 从左往右加节点
                int childrenSize = root.children.size();
                for (int j = 0; j < childrenSize; ++j) {
                    Node node = root.children.get(j);
                    if (node != null) {
                        queue.offer(node);
                    }
                }
            }
            ans.add(cueLevel);
        }
        return ans;
    }
    
}
```

> - ***Question 6: 二叉树的层序遍历***

```java
class Node {
    
    public int val;
    public Node left;
    public Node right;
    public Node next;
    
    public Node() {}
    
    public Node(int _val) {
        val = _val;
    }
    
    public Node(int _val, Node _left, Node _right, Node _next) {
        val = _val;
        left = _left;
        right = _right;
        next = _next;
    }
    
}

class Solution {
    
    // 我们使用next指针来实现链表的连接，然后用链表模拟队列
    private static class MyQueue {
        
        public Node head;
        public Node tail;
        public int size;
        
        public MyQueue() {
            head = null;
            tail = null;
            size = 0;
        }
        
        public boolean isEmpty() {
            return size == 0;
        }
        
        public void offer(Node node) {
            size++;
            if (head == null) {
                head = node;
                tail = node;
            } else {
                tail.next = node;
                tail = node;
            }
        }
        
        public Node poll() {
            size--;
            Node ans = head;
            head = head.next;
            // 在树上连接next指针时，节点已经弹出了，一定要销毁
            ans.next = null;
            return ans;
        }
        
    }
    
    public Node connect(Node root) {
        if (root == null) {
            return null;
        }
        MyQueue queue = new MyQueue();
        queue.offer(root);
        while (!queue.isEmpty()) {
            Node pre = null;
            int size = queue.size;
            for (int i = 0; i < size; i++) {
                Node cur = queue.poll();
                if (cur.left != null) {
                    queue.offer(cur.left);
                }
                if (cur.right != null) {
                    queue.offer(cur.right);
                }
                if (pre != null) {
                    // 不是第一个节点就连接
                    // 而且将之前的节点pre去连接现在的节点
                    // 因此最后一个节点不会有机会连接
                    pre.next = cur;
                }
                pre = cur;
            }
        }
        return root;
    }
    
}
```

---

> ***last change: 2024/3/28***

---
