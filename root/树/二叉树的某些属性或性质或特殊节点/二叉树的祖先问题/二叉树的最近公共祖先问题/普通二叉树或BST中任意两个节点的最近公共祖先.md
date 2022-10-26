# 普通二叉树或BST中任意两个节点的最近公共祖先

## [236. 二叉树的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree/)

## [面试题 04.08. 首个共同祖先](https://leetcode.cn/problems/first-common-ancestor-lcci/)

## [剑指 Offer 68 - II. 二叉树的最近公共祖先](https://leetcode.cn/problems/er-cha-shu-de-zui-jin-gong-gong-zu-xian-lcof/)

## [235. 二叉搜索树的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-search-tree/)

## [剑指 Offer 68 - I. 二叉搜索树的最近公共祖先](https://leetcode.cn/problems/er-cha-sou-suo-shu-de-zui-jin-gong-gong-zu-xian-lcof/)

> - ***Question 1***
>   - 给定一个二叉树的头节点 `root` , 找到该树中两个指定节点的最近公共祖先
>   - 对于有根树 `T` 的两个节点 `p` 、 `q` ，最近公共祖先表示为一个节点 `x` ，满足 `x` 是 `p、q` 的祖先且 `x` 的深度尽可能大（一个节点也可以是它自己的祖先）
> - ***Question 2***
>   - 给定一个二叉搜索树的头节点 `root` , 找到该树中两个指定节点的最近公共祖先
> - ***tips:***
>   - 树中节点数目在范围 `[2, 10^5]` 内
>   - `-109 <= Node.val <= 109`
>   - 所有 `Node.val` 互不相同
>   - `p != q`
>   - `p` 和 `q` 均存在于给定的二叉树/BST中

---

## *Java*

> - ***哈希表***
>   - 我们可以用哈希表存储所有节点的父节点，然后我们就可以利用节点的父节点信息从 `p` 结点开始不断往上跳，并记录已经访问过的节点，再从 `q` 节点开始不断往上跳，如果碰到已经访问过的节点，那么这个节点就是我们要找的最近公共祖先
>   - 从根节点开始遍历整棵二叉树，用 `HashMap` 记录每个节点的父节点指针
>   - 从 `p` 节点开始不断往它的祖先移动，并用 `HashSet` 记录已经访问过的祖先节点
>   - 同样，我们再从 `q` 节点开始不断往它的祖先移动，如果有祖先已经被访问过，即意味着这是 `p` 和 `q` 的深度最深的公共祖先，即 `LCA` 节点
>   - 这种思想本质上是找到从根节点到 `q` 和 `p` 节点的两条路径叠合在一起时的分叉点
> - ***TreeDP***
>   - 我们在利用后序遍历进行递归时会接受到左右子树处理好的信息，对于当前节点root，我们分析一下怎么构建它的信息类：
>     - 左子树或右子树其中一个发现了答案
>     - 左子树和右子树各发现了 `p` 和 `q` 中的一个
>     - `root` 是 `p` 或者 `q` 中的一个，左子树或者右子树发现了另外一个
>     - 否则的话就是子树上没有答案，或者是整棵树上没有p或者q，或者是 `p` 和 `q` 都没有
>   - 针对这些我们对子树要求 `3` 个信息，找到 `p` 否，找到 `q` 否，找到答案否，然后构建递归
>   - 我们将信息整合成子树上找没找到 `p` 或者 `q` ，将答案抽离成全局变量，移除信息类，将其简洁化
> - 两种算法的时间和空间复杂度都是 `O(N)` ，`N` 是二叉树的节点数
> - ***基于二叉搜索树的性质一次查找***
>   - 由于 `p` 和 `q` 的大小知道，那么当 `p` 和 `q` 的值都大于 `root` 的值时，就去右子树上查找，反之去左子树上查找，当 `root` 的值等于 `p`或者 `q` 中的一个，或者 `p` 和 `q` 分别在 `root` 的两边时（即 `root` 的值在 `p` 和 `q` 中间）， `root` 就是最近公共祖先
>   - 我们从根节点开始遍历：
>     - 如果当前节点的值大于 `p` 和 `q` 的值，说明 `p` 和 `q` 应该在当前节点的左子树，因此将当前节点移动到它的左子节点
>     - 如果当前节点的值小于 `p` 和 `q` 的值，说明 `p` 和 `q` 应该在当前节点的右子树，因此将当前节点移动到它的右子节点
>     - 如果当前节点的值不满足上述两条要求，那么说明当前节点就是分岔点。此时， `p` 和 `q` 要么在当前节点的不同的子树中，要么其中一个就是当前节点
>   - 时间复杂度是 `O(N)` ，`N` 是二叉树的节点数，空间复杂度是 `O(1)`

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
    
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        // 迭代
        // return lowestCommonAncestorByIteration(root, p, q);
        // 递归
        // return lowestCommonAncestorByRecursion(root, p, q).ans;
        // 精简版
        /*dfs(root, p, q);
        return ans;*/
        // 利用二叉搜素树的性质
        return lowestCommonAncestorByAVL(root, p, q);
    }
    
    // 利用二叉搜素树的性质直接查找
    private TreeNode lowestCommonAncestorByAVL(TreeNode root, TreeNode p, TreeNode q) {
        // 利用二叉搜索树的性质：小于的在左边，大于的在右边，我们可以迅速判断两个节点的大概位置
        if (root == null) {
            return null;
        }
        while (true) {
            // 在当前节点的左子树上
            if (p.val < root.val && q.val < root.val) {
                root = root.left;
            } else if (p.val > root.val && q.val > root.val) {
                // 在当前节点的右子树上
                root = root.right;
            } else {
                // 如果它分别在当前节点的左右子树上，或者自己是其中一个，那就是了
                break;
            }
        }
        return root;
    }
    
    private TreeNode lowestCommonAncestorByIteration(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null) {
            return null;
        }
        // value是key的父亲
        HashMap<TreeNode, TreeNode> fatherMap = new HashMap<>();
        fatherMap.put(root, null);
        // 利用一个递归函数遍历所有的节点将每一个节点与其父节点加入到HashMap中
        fillFatherMap(root, fatherMap);
        // 找到p的祖先路径，直到遇到根节点为止，全部存在一个Set中
        HashSet<TreeNode> pFatherSet = new HashSet<>();
        pFatherSet.add(p);
        while (fatherMap.get(p) != null) {
            p = fatherMap.get(p);
            pFatherSet.add(p);
        }
        // 遍历q的所有祖先，有一个在p的祖先Set中，那么第一次找到的那个就是最近公共祖先
        while (!pFatherSet.contains(q)) {
            q = fatherMap.get(q);
        }
        return q;
    }
    
    private void fillFatherMap(TreeNode root, HashMap<TreeNode, TreeNode> fatherMap) {
        // 子节点不为空就加入子节点与自己
        if (root.left != null) {
            fatherMap.put(root.left, root);
            fillFatherMap(root.left, fatherMap);
        }
        if (root.right != null) {
            fatherMap.put(root.right, root);
            fillFatherMap(root.right, fatherMap);
        }
    }
    
    // 信息类，包括了子树上有没有找到p节点和q节点以及p节点和q节点有没有在子树上就找到公共祖先
    private static class Info {
        
        public boolean findP;
        public boolean findQ;
        public TreeNode ans;
        
        public Info(boolean findP, boolean findQ, TreeNode ans) {
            this.findP = findP;
            this.findQ = findQ;
            this.ans = ans;
        }
        
    }
    
    // 递归
    private Info lowestCommonAncestorByRecursion(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null) {
            return new Info(false, false, null);
        }
        // 获取信息
        Info leftInfo = lowestCommonAncestorByRecursion(root.left, p, q);
        Info rightInfo = lowestCommonAncestorByRecursion(root.right, p, q);
        // 处理信息
        // 自己是pq中的一个或者子树上有pq，那么自己为根的整棵树上就发现了pq
        boolean findP = (root == p) || leftInfo.findP || rightInfo.findP;
        boolean findQ = (root == q) || leftInfo.findQ || rightInfo.findQ;
        // 假设没找到先
        // 满足以下六种情况就算找到
        TreeNode ans = null;
        // 首先是汇聚节点与root无关
        // pq都在左子树中，两者的最近公共祖先在左树上
        if (leftInfo.ans != null) {
            ans = leftInfo.ans;
        } else if (rightInfo.ans != null) {
            // 同理，最近公共祖先在右树上
            ans = rightInfo.ans;
        } else if (findP && findQ) {
            // 汇聚节点与root有关
            // 包括同时找到了两个节点但汇聚节点不在子树上，那么root必然是pq中的一个，pq自己可以作为自己的祖先
            // 还有就是pq分别在不同的子树上，那么root就是最近公共祖先
            ans = root;
        }
        // 还有一种情况是以root为根节点的整棵树没找全pq节点，那自然ans为null
        return new Info(findP, findQ, ans);
    }
    
    // 答案
    private TreeNode ans = null;
    
    // DFS
    private boolean dfs(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null) {
            return false;
        }
        // 左右子树上有没有找到p或者q节点
        boolean leftHasPOrQ = dfs(root.left, p, q);
        boolean rightHasPOrQ = dfs(root.right, p, q);
        if ((leftHasPOrQ && rightHasPOrQ) || ((root.val == p.val || root.val == q.val) && (leftHasPOrQ || rightHasPOrQ))) {
            ans = root;
        }
        // 返回整棵树上有没有p节点或者q节点
        return leftHasPOrQ || rightHasPOrQ || root.val == p.val || root.val == q.val;
    }
    
}
```

---

> ***last change: 2022/10/26***

---
