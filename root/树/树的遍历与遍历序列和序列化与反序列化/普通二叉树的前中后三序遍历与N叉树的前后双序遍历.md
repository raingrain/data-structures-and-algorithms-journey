# 普通二叉树的前中后三序遍历与N叉树的前后双序遍历

## [144. 二叉树的前序遍历](https://leetcode.cn/problems/binary-tree-preorder-traversal/)

## [94. 二叉树的中序遍历](https://leetcode.cn/problems/binary-tree-inorder-traversal/)

## [145. 二叉树的后序遍历](https://leetcode.cn/problems/binary-tree-postorder-traversal/)

## [589. N 叉树的前序遍历](https://leetcode.cn/problems/n-ary-tree-preorder-traversal/)

## [590. N 叉树的后序遍历](https://leetcode.cn/problems/n-ary-tree-postorder-traversal/)

> - ***Question 1***
>   - 给定二叉树的根节点 `root` ，返回它节点值的前序、中序与后序遍历序列
> - ***Question 2***
>   - 给定一个 `n` 叉树的根节点 `root` ，返回其节点值的前序和后序遍历序列

---

## *Java*

> - ***递归***
>   - 时间复杂度与空间复杂度都为 `O(n)` , `n` 为二叉树中的节点数
>   - 递归生成前序、中序、后序遍历的代码不同之处在于打印节点的位置不同，打印节点和对左右子树调用递归的顺序决定了生成怎么样的顺序
>   - 其核心原理在于递归（系统栈）能够保存子树的信息，而递归过程中会生成递归序，每个节点在递归序上都会出现 `3` 次，打印节点本质上是对递归序进行筛选的过程，比如树 `2 <- 1 -> 3` ，其递归序为 `1 2 2 2 1 3 3 3 1` ：
>     - 那么一个节点第 `1` 次出现在递归序中的时候就打印，生成的序列为先序遍历序列 `1 2 3`
>     - 那么一个节点第 `2` 次出现在递归序中的时候就打印，生成的序列为中序遍历序列 `2 1 3`
>     - 那么一个节点第 `3` 次出现在递归序中的时候就打印，生成的序列为后序遍历序列 `2 3 1`
> - ***迭代***
>   - 时间复杂度与空间复杂度都为 `O(n)` , `n` 为二叉树中的节点数
>   - 经典名言：任何递归都可以改成非递归
>   - 对于先序遍历来说：
>     - 准备一个栈用于循环，头节点先入栈：
>       - 弹出节点并加入到序列末尾
>       - 如果弹出的节点有右孩子，右孩子入栈
>       - 如果弹出的节点有左孩子，左孩子入栈
>       - 栈为空时循环结束
>   - 后序遍历的写法其实就是先序思路的逆向，先序代码中的入栈方式可以生成中左右的顺序，那么我们改变一下左右孩子的入栈顺序就可以生成中右左的顺序，而这正是后序遍历（左右中）的逆序，我们把结果利用栈来逆序即可：
>     - 准备两个栈，一个栈用于操作树，另一个栈先顶替掉结果链表用于存储操作结果，头节点入操作栈，进入循环：
>       - 弹出节点并入结果栈
>       - 如果弹出的节点有左孩子，左孩子入栈
>       - 如果弹出的节点有右孩子，右孩子入栈
>       - 栈为空时循环结束
>     - 把结果栈中的元素依次出栈并添加到结果链表的末尾
>   - 中序遍历的本质是二叉树可以被平行线从右上到左下划分为多组，对于一个节点来说，我们先处理它的左树（它的左树在栈中一定比它高，先被处理），处理完后再回来处理它，然后把它的右孩子入栈：
>     - 准备一个栈进入循环：
>       - 如果当前节点不为空，就把它入栈，它变成它的左孩子
>       - 如果当前节点为空，我们就把 `root` 变成栈顶，然后往结果中加入 `root的值` ， `root` 变成它的右孩子
>       - 当栈不为空或者用于循环的节点 `root` 不为空时继续循环
> - ***Morris遍历***
>   - 时间复杂度为 `O(n)` , 空间复杂度为 `O(1)` ， `n` 为二叉树中的节点数
>   - 在一次循环中，我们来到当前节点 `cur` ，开始时 `cur` 是头节点：
>     - 如果 `cur` 没有左孩子， `cur` 变成它的右孩子（即没有左子树的节点只被访问一次，有左子树的节点被访问两次）
>     - 如果 `cur` 有左孩子：
>       - 先找到它左子树的最右节点（左子树右边界上的最后一个节点）
>       - 如果左子树最右节点的右指针指向空，则说明其第一次来到 `cur` 节点，让其指向 `cur` ，然后 `cur` 等于它的左孩子
>       - 如果左子树最右节点的右指针指向 `cur` ，则说明其第二次来到 `cur` 节点，让其指向空，然后 `cur` 等于它的右孩子
>   - Morris遍历的本质是使用叶子节点的空闲右指针来指向将其作为左树最右节点的树的头节点，也就是实现了回溯的功能，并且Morris遍历会生成Morris序，我们对这个序列进行改造同样可以生成先序、中序、后序序列：
>     - 对于树 `4 <- 2 <- 1 -> 3 -> 5` ，其Morris序为 `1 2 4 2 1 3 5`
>     - 一个节点第一次出现在Morris序中的时候打印它，可以生成先序序列 `1 2 4 3 5`
>     - 一个节点最后一次出现在Morris序中的时候打印它，可以生成中序序列 `4 2 1 3 5`
>     - 对于可以出现两次的节点，在其第二次出现时逆序（逆序可以通过链表反转来实现，用栈实现会破坏空间复杂度）打印其左树右边界，并在循环结束后逆序打印整棵树的右边界，这样可以生成后序序列 `4 2 5 3 1`
> - 对于 `N` 叉树来说，层序遍历不能使用Morris遍历，且其没有中序遍历，其前序和后序遍历除了加节点的过程需要循环遍历 `children` 列表外，其他和二叉树相同

```java
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
    
    // 先序遍历
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> ans = new ArrayList<>();
        
        preorderTraversalByRecursion(root, ans);
        // preorderTraversalByIteration(root, ans);
        // preorderTraversalByMorris(root, ans);
        
        return ans;
    }
    
    // 递归生成先序序列
    public void preorderTraversalByRecursion(TreeNode root, List<Integer> ans) {
        if (root == null) {
            return;
        }
        ans.add(root.val);
        preorderTraversalByRecursion(root.left, ans);
        preorderTraversalByRecursion(root.right, ans);
    }
    
    // 迭代生成先序序列
    public void preorderTraversalByIteration(TreeNode root, List<Integer> ans) {
        if (root == null) {
            return;
        }
        Stack<TreeNode> stack = new Stack<>();
        stack.add(root);
        while (!stack.isEmpty()) {
            root = stack.pop();
            ans.add(root.val);
            // 先加右，再加左，弹出先弹左再弹右
            if (root.right != null) {
                stack.push(root.right);
            }
            if (root.left != null) {
                stack.push(root.left);
            }
        }
    }
    
    // Morris遍历生成先序序列
    public void preorderTraversalByMorris(TreeNode root, List<Integer> ans) {
        if (root == null) {
            return;
        }
        TreeNode leftTreeMostRight = null;
        while (root != null) {
            if (root.left != null) {
                leftTreeMostRight = root.left;
                while (leftTreeMostRight.right != null && leftTreeMostRight.right != root) {
                    leftTreeMostRight = leftTreeMostRight.right;
                }
                if (leftTreeMostRight.right == null) {
                    // 第一次出现时打印
                    ans.add(root.val);
                    leftTreeMostRight.right = root;
                    root = root.left;
                } else {
                    leftTreeMostRight.right = null;
                    root = root.right;
                }
            } else {
                // 无左子树只出现一次
                ans.add(root.val);
                root = root.right;
            }
        }
    }
    
     // 中序遍历
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> ans = new ArrayList<Integer>();
        
        inorderTraversalByRecursion(root, ans);
        // inorderTraversalByIteration(root, ans);
        // inorderTraversalByMorris(root, ans);
        return ans;
    }
    
    // 递归生成中序序列
    public void inorderTraversalByRecursion(TreeNode root, List<Integer> ans) {
        if (root == null) {
            return;
        }
        inorderTraversalByRecursion(root.left, ans);
        ans.add(root.val);
        inorderTraversalByRecursion(root.right, ans);
    }
    
    // 迭代生成中序序列
    public void inorderTraversalByIteration(TreeNode root, List<Integer> ans) {
        if (root == null) {
            return;
        }
        Stack<TreeNode> stack = new Stack<>();
        while (!stack.isEmpty() || root != null) {
            if (root != null) {
                // 有左子树就先处理左子树
                stack.push(root);
                root = root.left;
            } else {
                // 处理完左子树了，处理右子树
                root = stack.pop();
                ans.add(root.val);
                root = root.right;
            }
        }
    }
    
    // Morris遍历生成中序序列
    public void inorderTraversalByMorris(TreeNode root, List<Integer> ans) {
        if (root == null) {
            return;
        }
        TreeNode leftTreeMostRight;
        while (root != null) {
            if (root.left != null) {
                leftTreeMostRight = root.left;
                while (leftTreeMostRight.right != null && leftTreeMostRight.right != root) {
                    leftTreeMostRight = leftTreeMostRight.right;
                }
                if (leftTreeMostRight.right == null) {
                    leftTreeMostRight.right = root;
                    root = root.left;
                } else {
                    // 最后一次出现打印
                    ans.add(root.val);
                    leftTreeMostRight.right = null;
                    root = root.right;
                }
            } else {
                // 没有左子树的节点只出现一次
                ans.add(root.val);
                root = root.right;
            }
        }
    }
    
    // 后序遍历
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> ans = new ArrayList<Integer>();
        
        postorderTraversalByRecursion(root, ans);
        // postorderTraversalByIteration(root, ans);
        // postorderTraversalByRecursion(root, ans);
        
        return ans;
    }
    
    // 递归生成后序序列
    public void postorderTraversalByRecursion(TreeNode root, List<Integer> ans) {
        if (root == null) {
            return;
        }
        postorderTraversalByRecursion(root.left, ans);
        postorderTraversalByRecursion(root.right, ans);
        ans.add(root.val);
    }
    
    // 迭代生成后序序列
    public void postorderTraversalByIteration(TreeNode root, List<Integer> ans) {
        if (root == null) {
            return;
        }
        // 操作栈与结果栈
        Stack<TreeNode> operationStack = new Stack<>();
        Stack<TreeNode> resultStack = new Stack<>();
        operationStack.push(root);
        while (!operationStack.isEmpty()) {
            root = operationStack.pop();
            // 先加入到结果栈
            resultStack.push(root);
            if (root.left != null) {
                operationStack.push(root.left);
            }
            if (root.right != null) {
                operationStack.push(root.right);
            }
        }
        // 中右左 -> 左右中
        while (!resultStack.isEmpty()) {
            ans.add(resultStack.pop().val);
        }
    }
    
    // Morris遍历生成后序序列
    public void postorderTraversalByMorris(TreeNode root, List<Integer> ans) {
        if (root == null) {
            return;
        }
        TreeNode cur = root;
        TreeNode leftTreeMostRight;
        while (cur != null) {
            if (cur.left != null) {
                leftTreeMostRight = root.left;
                while (leftTreeMostRight.right != null && leftTreeMostRight.right != cur) {
                    leftTreeMostRight = leftTreeMostRight.right;
                }
                if (leftTreeMostRight.right == null) {
                    leftTreeMostRight.right = cur;
                    cur = cur.left;
                } else {
                    leftTreeMostRight.right = null;
                    // 节点第二次出现时打印
                    reverseAndPrintRightBoundary(cur.left, ans);
                    cur = cur.right;
                }
            } else {
                cur = cur.right;
            }
        }
        // 循环结束再打印
        reverseAndPrintRightBoundary(root, ans);
    }
    
    // 逆序打印右边界
    public void reverseAndPrintRightBoundary(TreeNode head, List<Integer> ans) {
        TreeNode tail = reverseLinkedList(head);
        TreeNode cur = tail;
        while (cur != null) {
            ans.add(cur.val);
            cur = cur.right;
        }
        reverseLinkedList(tail);
    }
    
    // 反转链表
    public TreeNode reverseLinkedList(TreeNode head) {
        TreeNode pre = null;
        TreeNode next;
        while (head != null) {
            next = head.right;
            head.right = pre;
            pre = head;
            head = next;
        }
        return pre;
    }
    
    // N叉树的前序遍历
    public List<Integer> preorder(Node root) {
        List<Integer> ans = new LinkedList<>();
        // preorderTraversalByRecursion(root, ans);
        preorderTraversalByIteration(root, ans);
        return ans;
    }
    
    // DFS
    private void preorderTraversalByRecursion(Node root, List<Integer> ans) {
        if (root == null) {
            return;
        }
        ans.add(root.val);
        for (Node child : root.children) {
            preorderTraversalByRecursion(child, ans);
        }
    }
    
    // BFS
    private void preorderTraversalByIteration(Node root, List<Integer> ans) {
        if (root == null) {
            return;
        }
        Deque<Node> stack = new ArrayDeque<>();
        stack.push(root);
        Node node;
        while (!stack.isEmpty()) {
            root = stack.pop();
            ans.add(root.val);
            for (int i = root.children.size() - 1; i >= 0; --i) {
                node = root.children.get(i);
                if (node != null) {
                    stack.push(node);
                }
            }
        }
    }
    
    // N叉树的后序遍历
    public List<Integer> postorder(Node root) {
        List<Integer> ans = new LinkedList<>();
        // postorderTraversalByRecursion(root, ans);
        // postorderTraversalByIteration(root, ans);
        postorderTraversalByRecursionToIteration(root, ans);
        return ans;
    }
    
    // 后序遍历DFS
    private void postorderTraversalByRecursion(Node root, List<Integer> ans) {
        if (root == null) {
            return;
        }
        for (Node child : root.children) {
            postorderTraversalByRecursion(child, ans);
        }
        ans.add(root.val);
    }
    
    // 用迭代模拟递归
    // 首先把根节点入栈，因为根节点是前序遍历中的第一个节点。随后每次我们找到栈顶节点u，如果当前节点的子节点没有遍历过，则应该先把u的所有子节点从右向左逆序压入栈中，这样出栈的节点则是顺序从左向右的，同时对节点u进行标记，表示该节点的子节点已经全部入栈；如果当前节点u为叶子节点或者当前节点的子节点已经全部遍历过，则从栈中弹出节点u，并记录节点u的值。例如u的子节点从左到右为v1、v2、v3，那么入栈的顺序应当为v3、v2、v1，这样就保证了下一个遍历到的节点（即u的左侧第一个孩子节点v1）出现在栈顶的位置。此时访问第一个子节点v1时，仍然按照此方法则会先访问v1的左侧第一个孩子节点
    private void postorderTraversalByRecursionToIteration(Node root, List<Integer> ans) {
        if (root == null) {
            return;
        }
        Deque<Node> stack = new ArrayDeque<>();
        // 标记有没有访问过
        HashSet<Node> set = new HashSet<>();
        stack.push(root);
        while (!stack.isEmpty()) {
            Node node = stack.peek();
            if (node.children.size() == 0 || set.contains(node)) {
                stack.pop();
                ans.add(node.val);
            } else {
                for (int i = node.children.size() - 1; i >= 0; --i) {
                    stack.push(node.children.get(i));
                }
                set.add(node);
            }
        }
    }
    
    // 后序遍历BFS
    private void postorderTraversalByIteration(Node root, List<Integer> ans) {
        if (root == null) {
            return;
        }
        Deque<Node> stack1 = new ArrayDeque<>();
        Deque<Integer> stack2 = new ArrayDeque<>();
        stack1.push(root);
        Node node;
        while (!stack1.isEmpty()) {
            root = stack1.pop();
            stack2.push(root.val);
            int childrenSize = root.children.size();
            for (int i = 0; i < childrenSize; ++i) {
                node = root.children.get(i);
                if (node != null) {
                    stack1.push(node);
                }
            }
        }
        while (!stack2.isEmpty()) {
            ans.add(stack2.pop());
        }
    }
    
}

```

---

> ***last change: 2022/10/26***

---
