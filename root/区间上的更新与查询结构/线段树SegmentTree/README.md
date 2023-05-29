# SegmentTree线段树代码模板

## [2236. 判断根结点是否等于子结点之和](https://leetcode.cn/problems/root-equals-sum-of-children/)

> - ***Question***
>   - 给你一个二叉树的根结点 `root` ，该二叉树由恰好 `3` 个结点组成：根结点、左子结点和右子结点。如果根结点值等于两个子结点值之和，返回 `true` ，否则返回 `false` 。
>   - ***tips:***
>     - 树只包含根结点、左子结点和右子结点
>     - `-100 <= Node.val <= 100`

## [303. 区域和检索 - 数组不可变](https://leetcode.cn/problems/range-sum-query-immutable/)

## [307. 区域和检索 - 数组可修改](https://leetcode.cn/problems/range-sum-query-mutable/)

> - ***Question 2***
>   - 给你一个数组 `nums` ，请你完成三类查询：
>     - 一类查询要求更新数组 `nums` 下标对应的值。
>     - 一类查询要求数组 `nums` 下标对应的值全部加上一个新的值。
>     - 一类查询要求返回数组 `nums` 中索引 `left` 和 `right` 之间（包含边界）的 `nums` 元素的和。
>   - 实现 `NumArray` 类（线段树的功能）：
>     - `NumArray(int[] nums)` 用整数数组 `nums` 初始化对象。
>     - `void update(int index, int val)` / `void update(int left, int right, int val)` 将对应区间内的值更新为 `val` 。
>     - `void add(int index, int val)` / `void add(int left, int right, int val)` 将对应区间内的值全部加上 `val` 。
>     - `int sumRange(int left, int right)` 返回数组 `nums` 中索引 `left` 和索引 `right` 之间的 `nums` 元素的和。
>   - ***tips:***
>     - `1 <= nums.length <= 3 * 10^4`
>     - `-10^5 <= nums[i] <= 10^5`
>     - `0 <= index < nums.length`
>     - `-100 <= val <= 100`
>     - `0 <= left <= right < nums.length`
>     - 调用 `update` 和 `add` 和 `sumRange` 方法次数不大于 `3 * 10^4`

---

## *Java*

> - ***线段树每个节点的性质***
>   - 根节点代表区域和，左右子节点代表分区域和。

```java
class Solution {

    public boolean checkTree(TreeNode root) {
        return root.val == root.left.val + root.right.val;
    }

}
```

> - ***数组实现线段树***
>   - 线段树用于处理左右半区获得的信息不用处理就可以加工出整个区域的正确信息的问题，如区间最值查询等。
>   - 线段树快的核心在于信息的懒处理。

```java
class NumArray {
    
    // 新数组长度
    private final int size;
    // 新数组
    private final int[] arr;
    // 对应区间的累加和
    // 线段树本体
    private final int[] sum;
    // add方法的懒加载信息
    // lazy[i]代表对应区间上每个数字要加的数
    private final int[] lazy;
    // change[i]指要change成的数
    private final int[] change;
    // 避免change[i]等于0的歧义，true表示要更新，false反之
    private final boolean[] isUpdate;
    
    public NumArray(int[] nums) {
        this.size = nums.length + 1;
        this.arr = new int[size];
        System.arraycopy(nums, 0, this.arr, 1, size - 1);
        // 算上各种不需要的节点树的所有层的满节点数不超过4N
        // 用来支持脑补概念中，某一个范围的累加和信息
        this.sum = new int[size * 4];
        // 用来支持脑补概念中，某一个范围沒有往下传递的累加任务
        this.lazy = new int[size * 4];
        // 用来支持脑补概念中，某一个范围有没有更新操作的任务
        this.change = new int[size * 4];
        // 用来支持脑补概念中，某一个范围更新任务，更新成了什么
        this.isUpdate = new boolean[size * 4];
        build(1, nums.length, 1);
    }
    
    private void getSum(int head) {
        sum[head] = sum[head * 2] + sum[head * 2 + 1];
    }
    
    // 在初始化阶段，先把sum数组，填好
    // 在arr[1 ~ arr.length - 1]范围上，去build sum数组，
    // head是这个范围在sum数组中的下标
    // int[] nums,
    private void build(int left, int right, int head) {
        if (left == right) {
            // sum[head] = nums[left - 1];
            sum[head] = arr[left];
            return;
        }
        int mid = (left + right) >> 1;
        build(left, mid, head * 2);
        build(mid + 1, right, head * 2 + 1);
        getSum(head);
    }
    
    // 懒信息推送
    // 之前的所有懒加载和懒更新，从父范围发给左右两个子范围
    private void pushDown(int head, int numOfLeftTree, int numOfRightTree) {
        // 更新的优先级高于加数
        if (isUpdate[head]) {
            // 把确定更新的信息推送到下一层
            isUpdate[head * 2] = true;
            isUpdate[head * 2 + 1] = true;
            // 把要更新成的数字推送到下一层
            change[head * 2] = change[head];
            change[head * 2 + 1] = change[head];
            // 因为需要更新，所以要add的数据直接被清空
            lazy[head * 2] = 0;
            lazy[head * 2 + 1] = 0;
            // 因为对应范围内全部都更新了，那么累加和自然就变成更新后的值乘以范围
            sum[head * 2] = change[head] * numOfLeftTree;
            sum[head * 2 + 1] = change[head] * numOfRightTree;
            isUpdate[head] = false;
        }
        // 需要对add数据进行懒更新
        // 刚更新完是不会有add数据存在的，在更新时都被清理了
        if (lazy[head] != 0) {
            // 懒加载信息被推送而来
            lazy[head * 2] += lazy[head];
            lazy[head * 2 + 1] += lazy[head];
            // 对应的区间和增加
            sum[head * 2] += lazy[head] * numOfLeftTree;
            sum[head * 2 + 1] += lazy[head] * numOfRightTree;
            // 懒加载信息已经推送到下一层，本层懒加载信息归0
            lazy[head] = 0;
        }
    }
    
    // 任务是在数组sum的L~R位置的数全部更新成val，当前来到的数组位置是head，其代表的区间范围是left~right
    private void update(int L, int R, int val, int left, int right, int head) {
        // 任务区间能把当前区间囊括住
        // 直接在这一层进行操作并更新懒信息
        if (L <= left && right <= R) {
            // 如果新老两个任务都能把当前区间囊括，就一起停在这一层
            isUpdate[head] = true;
            change[head] = val;
            sum[head] = val * (right - left + 1);
            lazy[head] = 0;
            return;
        }
        // 任务区间没能把当前区间囊括住
        int mid = (left + right) >> 1;
        // 新来的任务不能被head层拦截
        // 先把head层存下来的任务发到下一层
        // 然后再把新来的任务下发（因为新来的也不能被拦截）
        pushDown(head, mid - left + 1, right - mid);
        if (L <= mid) {
            update(L, R, val, left, mid, head * 2);
        }
        if (mid + 1 <= R) {
            update(L, R, val, mid + 1, right, head * 2 + 1);
        }
        getSum(head);
    }
    
    // 和update的含义一样，变成加上某个数
    private void add(int L, int R, int val, int left, int right, int head) {
        if (L <= left && right <= R) {
            sum[head] += val * (right - left + 1);
            lazy[head] += val;
            return;
        }
        int mid = (left + right) >> 1;
        pushDown(head, mid - left + 1, right - mid);
        if (L <= mid) {
            add(L, R, val, left, mid, head * 2);
        }
        if (mid + 1 <= R) {
            add(L, R, val, mid + 1, right, head * 2 + 1);
        }
        getSum(head);
    }
    
    // 查询区间和
    private int sumRange(int L, int R, int left, int right, int head) {
        // 任务区间能把当前区间囊括住
        // 直接查询累加和并返回
        if (L <= left && right <= R) {
            return sum[head];
        }
        int mid = (left + right) >> 1;
        pushDown(head, mid - left + 1, right - mid);
        int ans = 0;
        if (L <= mid) {
            ans += sumRange(L, R, left, mid, head * 2);
        }
        if (mid + 1 <= R) {
            ans += sumRange(L, R, mid + 1, right, head * 2 + 1);
        }
        return ans;
    }
    
    // 对外提供的方法的索引是原数组，要加1才能运用于线段树的相关数组

    public void update(int left, int right, int val) {
        update(left + 1, right + 1, val, 1, size - 1, 1);
    }
    
    public void update(int index, int val) {
        update(index + 1, index + 1, val, 1, size - 1, 1);
    }
    
    public void add(int left, int right, int val) {
        add(left + 1, right + 1, val, 1, size - 1, 1);
    }
    
    public void add(int index, int val) {
        add(index + 1, index + 1, val, 1, size - 1, 1);
    }
    
    // 查询指定区间累加和
    public int sumRange(int left, int right) {
        return sumRange(left + 1, right + 1, 1, size - 1, 1);
    }
    
}
```

> - ***动态开点实现线段树***
>   - 有需要才创建

```java
class DynamicSegmentTree {
    
    // 节点与范围解耦，范围由函数控制
    private static class DynamicSegmentTreeNode {
        
        public int sum;
        public int lazy;
        public int change;
        public boolean update;
        public DynamicSegmentTreeNode left;
        public DynamicSegmentTreeNode right;
        
    }
    
    private final DynamicSegmentTreeNode root;
    private final int size;
    // private final int[] origin;
    
    public DynamicSegmentTree(int size) {
        root = new DynamicSegmentTreeNode();
        this.size = size;
        // this.origin = origin;
        // buildTree();
    }
    
    /*private void buildTree() {
        for (int i = 0; i < origin.length; i++) {
            add(root, 1, origin.length, i + 1, i + 1, origin[i]);
        }
    }*/
    
    private void getSum(DynamicSegmentTreeNode node) {
        node.sum = node.left.sum + node.right.sum;
    }
    
    private void pushDown(DynamicSegmentTreeNode node, int leftSize, int rightSize) {
        // 没有节点要创建，不然没法往下推送
        if (node.left == null) {
            node.left = new DynamicSegmentTreeNode();
        }
        if (node.right == null) {
            node.right = new DynamicSegmentTreeNode();
        }
        if (node.update) {
            node.left.update = true;
            node.right.update = true;
            node.left.change = node.change;
            node.right.change = node.change;
            node.left.lazy = 0;
            node.right.lazy = 0;
            node.left.sum = node.change * leftSize;
            node.right.sum = node.change * rightSize;
            node.update = false;
        }
        if (node.lazy != 0) {
            node.left.lazy += node.lazy;
            node.right.lazy += node.lazy;
            node.left.sum += node.lazy * leftSize;
            node.right.sum += node.lazy * rightSize;
            node.lazy = 0;
        }
    }
    
    private void update(DynamicSegmentTreeNode node, int left, int right, int start, int end, int value) {
        if (start <= left && right <= end) {
            node.update = true;
            node.change = value;
            node.sum = value * (right - left + 1);
            node.lazy = 0;
        } else {
            int mid = left + (right - left) / 2;
            pushDown(node, mid - left + 1, right - mid);
            if (start <= mid) {
                update(node.left, left, mid, start, end, value);
            }
            if (end >= mid + 1) {
                update(node.right, mid + 1, right, start, end, value);
            }
            getSum(node);
        }
    }
    
    private void add(DynamicSegmentTreeNode node, int left, int right, int start, int end, int value) {
        if (start <= left && right <= end) {
            node.sum += value * (right - left + 1);
            node.lazy += value;
        } else {
            int mid = left + (right - left) / 2;
            pushDown(node, mid - left + 1, right - mid);
            if (start <= mid) {
                add(node.left, left, mid, start, end, value);
            }
            if (end >= mid + 1) {
                add(node.right, mid + 1, right, start, end, value);
            }
            getSum(node);
        }
    }
    
    private int sumRange(DynamicSegmentTreeNode node, int left, int right, int start, int end) {
        if (start <= left && right <= end) {
            return node.sum;
        } else {
            int mid = left + (right - left) / 2;
            pushDown(node, mid - left + 1, right - mid);
            int ans = 0;
            if (start <= mid) {
                ans += sumRange(node.left, left, mid, start, end);
            }
            if (end >= mid + 1) {
                ans += sumRange(node.right, mid + 1, right, start, end);
            }
            return ans;
        }
    }
    
    // 以下传进来的全都是原始的
    // 单点更新
    public void update(int index, int value) {
        update(index, index, value);
    }
    
    // 区域更新
    public void update(int start, int end, int value) {
        update(root, 1, size, start + 1, end + 1, value);
    }
    
    // 单点加
    public void add(int index, int value) {
        add(index, index, value);
    }
    
    // 区域加
    public void add(int index, int end, int value) {
        add(root, 1, size, index + 1, end + 1, value);
    }
    
    // 区域查询
    public int sumRange(int start, int end) {
        return sumRange(root, 1, size, start + 1, end + 1);
    }
    
}

class NumArray {
    
    private final DynamicSegmentTree segmentTree;
    
    public NumArray(int[] nums) {
        segmentTree = new DynamicSegmentTree(nums.length);
        // 初始化线段树
        for (int i = 0; i < nums.length; i++) {
            segmentTree.add(i, nums[i]);
        }
    }
    
    public void update(int index, int val) {
        segmentTree.update(index, val);
    }
    
    public int sumRange(int left, int right) {
        return segmentTree.sumRange(left, right);
    }
    
}
```

---

> ***last change: 2023/4/27***

---
