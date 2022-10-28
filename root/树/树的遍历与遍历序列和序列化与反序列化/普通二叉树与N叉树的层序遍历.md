# 普通二叉树与N叉树的层序遍历

## [剑指 Offer 32 - I. 从上到下打印二叉树](https://leetcode.cn/problems/cong-shang-dao-xia-da-yin-er-cha-shu-lcof/)

## [102. 二叉树的层序遍历](https://leetcode.cn/problems/binary-tree-level-order-traversal/)

## [剑指 Offer 32 - II. 从上到下打印二叉树 II](https://leetcode.cn/problems/cong-shang-dao-xia-da-yin-er-cha-shu-ii-lcof/)

## [107. 二叉树的层序遍历 II](https://leetcode.cn/problems/binary-tree-level-order-traversal-ii/)

## [103. 二叉树的锯齿形层序遍历](https://leetcode.cn/problems/binary-tree-zigzag-level-order-traversal/)

## [剑指 Offer 32 - III. 从上到下打印二叉树 III](https://leetcode.cn/problems/cong-shang-dao-xia-da-yin-er-cha-shu-iii-lcof/)

## [429. N 叉树的层序遍历](https://leetcode.cn/problems/n-ary-tree-level-order-traversal/)

> - ***Question 1***
>   - 实现二叉树的层序遍历，返回一个存储全部节点的数组。
> - ***Question 2***
>   - 实现二叉树的层序遍历，每一层的节点用一个链表来存储，返回由每层链表的头节点组成的链表（二维链表）。
> - ***Question 3***
>   - 实现二叉树的逆层序遍历（即按从叶子节点所在层到根节点所在的层，逐层从左向右遍历），返回和***Question 2***相同的结构。
> - ***Question 4***
>   - 实现二叉树的锯齿形层序遍历（之字形顺序）（即先从左往右（根节点所在层），再从右往左进行下一层遍历，以此类推，层与层之间交替进行），返回和***Question 2***相同的结构。
> - ***Question 5***
>   - 给定一个 `N` 叉树，返回其节点值的层序遍历。

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

---

> ***last change: 2022/10/28***

---
