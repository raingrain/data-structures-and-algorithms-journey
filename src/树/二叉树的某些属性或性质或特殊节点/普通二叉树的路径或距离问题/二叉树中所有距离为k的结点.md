# 二叉树中所有距离为k的结点

## [863. 二叉树中所有距离为 K 的结点](https://leetcode.cn/problems/all-nodes-distance-k-in-binary-tree/)

> - ***Question***
>   - 给定一个二叉树的根结点 `root` ， 一个目标结点 `target` ，和一个整数值 `k` 。
>   - 返回到目标结点 `target` 距离为 `k` 的所有结点的值的列表。答案可以以任何顺序返回。
>   - ***tips:***
>     - 节点数在 `[1, 500]` 范围内
>     - `0 <= Node.val <= 500`
>     - `Node.val` 中所有值 不同
>     - 目标结点 `target` 是树上的结点
>     - 0 <= k <= 1000`

---

## *Java*

> - ***树状DP + 宽度优先遍历***

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
    
    public List<Integer> distanceK(TreeNode root, TreeNode target, int k) {
        // 父节点表
        HashMap<TreeNode, TreeNode> parents = new HashMap<>();
        // 根节点的父节点为空
        parents.put(root, null);
        // 创建整棵树的父节点表
        createParentsMap(root, parents);
        // 用于宽度优先遍历的队列
        ArrayDeque<TreeNode> queue = new ArrayDeque<>();
        // 因为有一个找父节点的过程，需要排除已经遍历过的节点
        HashSet<TreeNode> visited = new HashSet<>();
        // 从目标节点开始进行k次宽度优先遍历
        queue.offer(target);
        visited.add(target);
        // 当前来到的层数
        int cueLevel = 0;
        // 结果数组
        List<Integer> ans = new ArrayList<>();
        // 队列不为空或距离小于k
        while (!queue.isEmpty() && cueLevel <= k) {
            // 当前层节点个数，即要出队几次
            int size = queue.size();
            while (size-- > 0) {
                TreeNode node = queue.poll();
                if (cueLevel == k) {
                    // 当前层就是距离target k的层，每次都要加到答案中
                    ans.add(node.val);
                }
                // 左孩子不为空且没遍历过
                if (node.left != null && !visited.contains(node.left)) {
                    queue.offer(node.left);
                    visited.add(node.left);
                }
                // 右孩子不为空且没遍历过
                if (node.right != null && !visited.contains(node.right)) {
                    queue.offer(node.right);
                    visited.add(node.right);
                }
                // 父节点不为空且没遍历过
                if (parents.get(node) != null && !visited.contains(parents.get(node))) {
                    queue.offer(parents.get(node));
                    visited.add(parents.get(node));
                }
            }
            // 当前层++
            cueLevel++;
        }
        return ans;
    }
    
    // 构建父节点表
    public void createParentsMap(TreeNode node, HashMap<TreeNode, TreeNode> parents) {
        if (node == null) {
            return;
        }
        // 在当前递归设置子节点的父
        if (node.left != null) {
            parents.put(node.left, node);
            createParentsMap(node.left, parents);
        }
        if (node.right != null) {
            parents.put(node.right, node);
            createParentsMap(node.right, parents);
        }
    }
    
}
```

---

> ***last change: 2023/3/9***

---
