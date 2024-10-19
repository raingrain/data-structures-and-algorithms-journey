# 基于普通二叉树实现N叉树的序列化与反序列化

## [431. 将 N 叉树编码为二叉树](https://leetcode.cn/problems/encode-n-ary-tree-to-binary-tree/)

> - ***Question***
>   - 请实现 `Codec` 类，实现 `encode` 和 `decode` 方法，用于N叉树序列化为二叉树，以及二叉树序列化为N叉树。

---

## *Java*

> - ***递归***
>   - 对于N叉树的一个节点来说，其序列化成一颗二叉树的方式是将其所有孩子（ `List<Node> children` 中定义的）挂在其变成二叉树的节点后的左树右边界上。
>   - 序列化和反序列化采用深度优先的方式（看代码）。

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

class Codec {
    
    public TreeNode encode(Node root) {
        if (root == null) {
            return null;
        }
        // 建好二叉树的头节点
        TreeNode head = new TreeNode(root.val);
        // 我的左树右边界上挂着我的孩子
        head.left = encodeByRecursion(root.children);
        return head;
    }
    
    public TreeNode encodeByRecursion(List<Node> children) {
        // 哨兵守卫避免讨论
        TreeNode dummy = new TreeNode(0, null, null);
        TreeNode cur = dummy;
        for (Node child : children) {
            TreeNode node = new TreeNode(child.val);
            cur.right = node;
            cur = node;
            // 深度优先遍历，不是挂好一层再挂下一层，是先把我的孩子挂完，再往我的右边挂我的兄弟
            cur.left = encodeByRecursion(child.children);
        }
        // 把整条右边界返回
        return dummy.right;
    }
    
    public Node decode(TreeNode root) {
        if (root == null) {
            return null;
        }
        return new Node(root.val, decodeByRecursion(root.left));
    }
    
    // 给定二叉树节点root，将其在N叉树中的孩子列表返回
    public List<Node> decodeByRecursion(TreeNode root) {
        List<Node> children = new LinkedList<>();
        while (root != null) {
            // 先把我创建好
            Node cur = new Node(root.val, decodeByRecursion(root.left));
            // 再把我加到孩子列表上
            children.add(cur);
            // 再处理我的兄弟节点
            root = root.right;
        }
        // 返回列表用于我的父亲节点的构建
        return children;
    }
    
}
```
