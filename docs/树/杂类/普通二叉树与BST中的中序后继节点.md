# 普通二叉树与BST中的中序后继节点

## [285. 二叉搜索树中的中序后继](https://leetcode.cn/problems/inorder-successor-in-bst/)

## [面试题 04.06. 后继者](https://leetcode.cn/problems/successor-lcci/)

## [剑指 Offer II 053. 二叉搜索树中的中序后继](https://leetcode.cn/problems/P5rCT8/)

> - **Question**
>   - 给定一棵二叉搜索树和其中的一个节点 `p` ，找到该节点在树中的中序后继。如果节点没有中序后继，请返回 `null` 。
>   - 节点 `p` 的后继是值比 `p.val` 大的节点中键值最小的节点，即按中序遍历的顺序节点 `p` 的下一个节点。

## Java

> - **普通二叉树中的中序后继节点**
>   - 如果一个节点有右孩子，那么其中序后继节点就是其右树上的最左节点。
>   - 如果一个节点没有右孩子，那么其中序后继就是离该节点最近并将该节点当作左树上的最右节点的祖先节点。
> - **BST中的中序后继节点**
>   - 二叉搜索树的一个性质是中序遍历序列单调递增，因此二叉搜索树中的节点 `p` 的中序后继满足以下条件：
>     - 中序后继的节点值大于 `p` 的节点值。
>     - 中序后继是节点值大于 `p` 的节点值的所有节点中节点值最小的一个节点。
>   - 利用二叉搜索树的性质，可以在不做中序遍历的情况下找到节点 `p` 的中序后继：
>     - 如果节点 `p` 的右子树不为空，则节点 `p` 的中序后继在其右子树中，在其右子树中定位到最左边的节点，即为节点 `p` 的中序后继。
>     - 如果节点 `p` 的右子树为空，则需要从根节点开始遍历寻找节点 `p` 的祖先节点。
>   - 将答案初始化为 `null` ，用 `node` 表示遍历到的节点，初始时 `node = root` ，每次比较 `node` 的节点值和 `p` 的节点值，执行相应操作：
>     - 如果 `node` 的节点值大于 `p` 的节点值，则 `p` 的中序后继可能是 `node` 或者在 `node` 的左子树中，因此用 `node` 更新答案，并将 `node` 移动到其左子节点继续遍历。
>     - 如果 `node` 的节点值小于或等于 `p` 的节点值，则 `p` 的中序后继可能在 `node` 的右子树中，因此将 `node` 移动到其右子节点继续遍历。
>   - 由于在遍历过程中，当且仅当 `node` 的节点值大于 `p` 的节点值的情况下，才会用 `node` 更新答案，因此当节点 `p` 有中序后继时一定可以找到中序后继，当节点 `p` 没有中序后继时答案一定为 `null` 。
>   - 时间复杂度为 `O(n)` ，空间复杂度都为 `O(1)` ，最坏情况下单边树遍历 `n` 个节点。

```java
// 具有父亲指针的节点
class NodeHasFather {
    
    int val;
    NodeHasFather left;
    NodeHasFather right;
    NodeHasFather father;
    
    NodeHasFather() {}
    
    NodeHasFather(int val) {this.val = val;}
    
    NodeHasFather(int val, NodeHasFather left, NodeHasFather right) {
        this.val = val;
        this.left = left;
        this.right = right;
    }
    
    NodeHasFather(int val, NodeHasFather left, NodeHasFather right, NodeHasFather father) {
        this.val = val;
        this.left = left;
        this.right = right;
        this.father = father;
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
    
    public NodeHasFather inorderSuccessor(NodeHasFather node) {
        if (node == null) {
            return null;
        }
        if (node.right != null) {
            return getRightTreeMostLeftNode(node.right);
        } else {
            // 没有右孩子就往上找，找到该节点位于左树姿态这一状态的第一个终结者
            NodeHasFather father = node.father;
            while (father != null && father.right == node) {
                node = father;
                father = node.father;
            }
            return father;
        }
    }
    private NodeHasFather getRightTreeMostLeftNode(NodeHasFather node) {
        if (node == null) {
            return null;
        }
        while (node.left != null) {
            node = node.left;
        }
        return node;
    }
    
    private TreeNode getRightTreeMostLeftNode(TreeNode node) {
        if (node == null) {
            return null;
        }
        while (node.left != null) {
            node = node.left;
        }
        return node;
    }
    
    public TreeNode inorderSuccessor(TreeNode root, TreeNode p) {
        return inorderSuccessorByInorderTraversal(root, p);
        // return inorderSuccessorByBST(root, p);
    }
    
    public TreeNode inorderSuccessorByInorderTraversal(TreeNode root, TreeNode p) {
        if (root == null) {
            return null;
        }
        Deque<TreeNode> stack = new ArrayDeque<>();
        boolean isFindP = false;
        while (!stack.isEmpty() || root != null) {
            if (root != null) {
                stack.push(root);
                root = root.left;
            } else {
                root = stack.pop();
                if (isFindP) {
                    return root;
                }
                if (root.val == p.val) {
                    isFindP = true;
                }
                root = root.right;
            }
        }
        return null;
    }
    
    public TreeNode inorderSuccessorByBST(TreeNode root, TreeNode p) {
        if (root == null || p == null) {
            return null;
        }
        if (p.right != null) {
            return getRightTreeMostLeftNode(p.right);
        } else {
            TreeNode successor = null;
            TreeNode node = root;
            while (node != null) {
                if (node.val > p.val) {
                    successor = node;
                    node = node.left;
                } else {
                    node = node.right;
                }
            }
            return successor;
        }
    }
    
}
```
