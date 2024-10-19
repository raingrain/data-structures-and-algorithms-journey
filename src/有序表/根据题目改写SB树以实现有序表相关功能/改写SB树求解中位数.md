# 改写SB树求解中位数

## [480. 滑动窗口中位数](https://leetcode.cn/problems/sliding-window-median/)

> - ***Question 1***
>   - 给你一个数组 `nums` ，有一个长度为 `k` 的窗口从最左端滑动到最右端。窗口中有 `k` 个数，每次窗口向右移动 `1` 位。
>   - 请找出每次窗口移动后得到的新窗口中元素的中位数，并输出由它们组成的数组。
>   - ***tips:***
>     - 你可以假设 `k` 始终有效，即 `k` 始终小于等于输入的非空数组的元素个数。
>     - 与真实值误差在 `10 ^ -5` 以内的答案将被视作正确答案。

## [295. 数据流的中位数](https://leetcode.cn/problems/find-median-from-data-stream/)

## [面试题 17.20. 连续中值](https://leetcode.cn/problems/continuous-median-lcci/)

## [剑指 Offer 41. 数据流中的中位数](https://leetcode.cn/problems/shu-ju-liu-zhong-de-zhong-wei-shu-lcof/)

> - ***Question 2***
>   - 设计一个支持以下两种操作的数据结构：
>     - `void addNum(int num)` - 从数据流中添加一个整数到数据结构中。
>     - `double findMedian()` - 返回目前所有元素的中位数。
>   - 进阶:
>     - 如果数据流中所有整数都在 `0` 到 `100` 范围内，你将如何优化你的算法：
>       - 如果数据流中所有整数都在 `0` 到 `100` 范围内，那么我们可以利用计数排序统计每一类数的数量，并使用双指针维护中位数。
>     - 如果数据流中 `99%` 的整数都在 `0` 到 `100` 范围内，你将如何优化你的算法：
>       - 如果数据流中 `99%` 的整数都在 `0` 到 `100` 范围内，那么我们依然利用计数排序统计每一类数的数量，并使用双指针维护中位数。对于超出范围的数，我们可以单独进行处理，建立两个数组，分别记录小于 `0` 的部分的数的数量和大于 `100` 的部分的数的数量即可。当小部分时间，中位数不落在区间 `[0,100]` 中时，我们在对应的数组中暴力检查即可。

---

## *Java*

> - ***Question 1 & Question 2: SB树的改写***
>   - 这种问题的特点就是，求解答案时需要的数据是范围确定且有序的，求解的过程有容易理解的暴力解但时间复杂度高。这时候我们可以利用SB树改写容易的特点，用改写的SB树来存储范围内的数据，又因为是搜索二叉树，有序也可以满足，我们只需要对数据和最终需要的解题思路改写节点、增\删方法即可，形成一个时间复杂度为 `O(logN)` 的黑盒。
>   - SB树是不支持加入相同 `key` 的，如果想重复加入数字作为 `key` （比如用SB树维护一个数组），我们可以用 `index` 位置作为标识，然后用一个对象把它们包装起来，以值作为排序的第一关键字，值相同时以索引为第二排序关键字（因为数学意义上的中位数求解是在有序序列的情况下计算的，我们维护一个要计算中位数的序列，自然要排好序），当然如果只要求重复加而不需要识别，也可以像[区间和的个数](区间和的个数.md)一样用一个数据项 `all` 存储路过的节点数。
>   - 对于题目我们需要维护一个存储要求解中位数的序列（窗口），能够一个个加入重复数据，能够一个个删除数据，能够获得中点或者上下中点处的值，要求中位数直接拿出中点处的数据即可。

```java
// 滑动窗口中位数
class Solution {
    
    public double[] medianSlidingWindow(int[] nums, int k) {
        SBTree<Key> sbTree = new SBTree<>();
        // 把前k - 1个加入先
        for (int i = 0; i < k - 1; i++) {
            sbTree.add(new Key(i, nums[i]));
        }
        double[] ans = new double[nums.length - k + 1];
        int index = 0;
        for (int i = k - 1; i < nums.length; i++) {
            // 加上这一轮的末尾
            sbTree.add(new Key(i, nums[i]));
            // 严格意义上的中位数
            if (sbTree.size() % 2 == 0) {
                // 上下中位数之和除以2
                Key upMidKey = sbTree.getIndexKey(sbTree.size() / 2 - 1);
                Key downMidKey = sbTree.getIndexKey(sbTree.size() / 2);
                ans[index++] = ((double) upMidKey.value + (double) downMidKey.value) / 2;
            } else {
                Key mid = sbTree.getIndexKey(sbTree.size() / 2);
                ans[index++] = mid.value;
            }
            // 移除这一轮的开头
            sbTree.remove(new Key(i - k + 1, nums[i - k + 1]));
        }
        return ans;
    }
    
}

// 数据流的中位数
/**
 * Your MedianFinder object will be instantiated and called as such:
 * MedianFinder obj = new MedianFinder();
 * obj.addNum(num);
 * double param_2 = obj.findMedian();
 */
class MedianFinder {
    
    // 将加入的数字模拟成数组序列，index是它们的索引
    private int index;
    private final SBTree<Key> map;
    
    /**
     * initialize your data structure here.
     */
    public MedianFinder() {
        index = 0;
        map = new SBTree<>();
    }
    
    public void addNum(int num) {
        map.add(new Key(index++, num));
    }
    
    public double findMedian() {
        if (map.size() % 2 == 0) {
            // 上下中位数之和除以2
            Key upMidKey = map.getIndexKey(map.size() / 2 - 1);
            Key downMidKey = map.getIndexKey(map.size() / 2);
            return ((double) upMidKey.value + (double) downMidKey.value) / 2;
        } else {
            Key mid = map.getIndexKey(map.size() / 2);
            return mid.value;
        }
    }
    
}

// SB树的节点
class SBNode<K extends Comparable<K>> {
    
    public K key;
    public SBNode<K> left;
    public SBNode<K> right;
    // 平衡因子：节点数
    public int size;
    
    public SBNode(K key) {
        this.key = key;
        this.size = 1;
    }
    
}

// 由于数组中有重复值，我们把key用一个类包装起来
class Key implements Comparable<Key> {
    
    // 在数组中的位置
    public int index;
    // 实际值
    public int value;
    
    public Key(int index, int value) {
        this.index = index;
        this.value = value;
    }
    
    // 判断两个key是否相等
    // 重写方法
    @Override
    public int compareTo(Key otherKey) {
        return value != otherKey.value ? Integer.compare(value, otherKey.value) : Integer.compare(index, otherKey.index);
    }
    
}

// SB树
class SBTree<K extends Comparable<K>> {
    
    private SBNode<K> root;
    
    private int getSize(SBNode<K> head) {
        return 1 + (head.left != null ? head.left.size : 0) + (head.right != null ? head.right.size : 0);
    }
    
    private SBNode<K> rightRotate(SBNode<K> head) {
        SBNode<K> left = head.left;
        head.left = left.right;
        left.right = head;
        left.size = head.size;
        head.size = getSize(head);
        return left;
    }
    
    private SBNode<K> leftRotate(SBNode<K> head) {
        SBNode<K> rightNode = head.right;
        head.right = rightNode.left;
        rightNode.left = head;
        rightNode.size = head.size;
        head.size = getSize(head);
        return rightNode;
    }
    
    private SBNode<K> keepBalance(SBNode<K> cur) {
        if (cur == null) {
            return null;
        }
        int leftSize = cur.left != null ? cur.left.size : 0;
        int leftLeftSize = cur.left != null && cur.left.left != null ? cur.left.left.size : 0;
        int leftRightSize = cur.left != null && cur.left.right != null ? cur.left.right.size : 0;
        int rightSize = cur.right != null ? cur.right.size : 0;
        int rightLeftSize = cur.right != null && cur.right.left != null ? cur.right.left.size : 0;
        int rightRightSize = cur.right != null && cur.right.right != null ? cur.right.right.size : 0;
        if (leftLeftSize > rightSize) {
            cur = rightRotate(cur);
            cur.right = keepBalance(cur.right);
            cur = keepBalance(cur);
        } else if (leftRightSize > rightSize) {
            cur.left = leftRotate(cur.left);
            cur = rightRotate(cur);
            cur.left = keepBalance(cur.left);
            cur.right = keepBalance(cur.right);
            cur = keepBalance(cur);
        } else if (rightRightSize > leftSize) {
            cur = leftRotate(cur);
            cur.left = keepBalance(cur.left);
            cur = keepBalance(cur);
        } else if (rightLeftSize > leftSize) {
            cur.right = rightRotate(cur.right);
            cur = leftRotate(cur);
            cur.left = keepBalance(cur.left);
            cur.right = keepBalance(cur.right);
            cur = keepBalance(cur);
        }
        return cur;
    }
    
    private SBNode<K> add(SBNode<K> head, K key) {
        if (head == null) {
            return new SBNode<>(key);
        } else {
            head.size++;
            if (key.compareTo(head.key) < 0) {
                head.left = add(head.left, key);
            } else {
                head.right = add(head.right, key);
            }
            return keepBalance(head);
        }
    }
    
    private SBNode<K> delete(SBNode<K> head, K key) {
        head.size--;
        if (key.compareTo(head.key) > 0) {
            head.right = delete(head.right, key);
        } else if (key.compareTo(head.key) < 0) {
            head.left = delete(head.left, key);
        } else {
            if (head.left == null && head.right == null) {
                head = null;
            } else if (head.left == null && head.right != null) {
                head = head.right;
            } else if (head.left != null && head.right == null) {
                head = head.left;
            } else {
                SBNode<K> pre = null;
                SBNode<K> rightMostLeft = head.right;
                rightMostLeft.size--;
                while (rightMostLeft.left != null) {
                    pre = rightMostLeft;
                    rightMostLeft = rightMostLeft.left;
                    rightMostLeft.size--;
                }
                if (pre != null) {
                    pre.left = rightMostLeft.right;
                    rightMostLeft.right = head.right;
                }
                rightMostLeft.left = head.left;
                rightMostLeft.size = rightMostLeft.left.size + (rightMostLeft.right == null ? 0 : rightMostLeft.right.size) + 1;
                head = rightMostLeft;
            }
        }
        return head;
    }
    
    private SBNode<K> findNearestNode(K key) {
        SBNode<K> pre = root;
        SBNode<K> cur = root;
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
    
    private SBNode<K> getIndex(SBNode<K> head, int k) {
        if (k == (head.left != null ? head.left.size : 0) + 1) {
            return head;
        } else if (k <= (head.left != null ? head.left.size : 0)) {
            return getIndex(head.left, k);
        } else {
            return getIndex(head.right, k - (head.left != null ? head.left.size : 0) - 1);
        }
    }
    
    public int size() {
        return root == null ? 0 : root.size;
    }
    
    public boolean isEmpty() {
        return root == null;
    }
    
    public boolean containsKey(K key) {
        if (key == null) {
            throw new RuntimeException("invalid parameter.");
        } else {
            SBNode<K> nearestNode = findNearestNode(key);
            return nearestNode != null && key.compareTo(nearestNode.key) == 0;
        }
    }
    
    public void add(K key) {
        if (key == null) {
            throw new RuntimeException("invalid parameter.");
        } else {
            SBNode<K> nearestNode = findNearestNode(key);
            if (nearestNode == null || key.compareTo(nearestNode.key) != 0) {
                root = add(root, key);
            }
        }
    }
    
    public void remove(K key) {
        if (key == null) {
            throw new RuntimeException("invalid parameter.");
        }
        if (containsKey(key)) {
            root = delete(root, key);
        }
    }
    
    // 获取中点或者上下中点值，求解SB树维护的序列的关键字的重要方法
    public K getIndexKey(int index) {
        if (index < 0 || index >= this.size()) {
            throw new RuntimeException("invalid parameter.");
        } else {
            return getIndex(root, index + 1).key;
        }
    }
    
}
```

> - ***Question 2: 大根堆 + 小根堆***

```java
class MedianFinder {
    
    private final PriorityQueue<Integer> maxHeap;
    private final PriorityQueue<Integer> minHeap;
    
    public MedianFinder() {
        maxHeap = new PriorityQueue<>((a, b) -> b - a);
        minHeap = new PriorityQueue<>(Comparator.comparingInt(a -> a));
    }
    
    public void addNum(int num) {
        // 大根堆为空或者当前数字比大根堆堆顶小就入大根堆
        if (maxHeap.isEmpty() || maxHeap.peek() >= num) {
            maxHeap.add(num);
        } else {
            // 不然就进小根堆
            minHeap.add(num);
        }
        balance();
    }
    
    // 这种双堆结构保证大根堆是数据流中小的一部分，小根堆是大的一部分
    public double findMedian() {
        if (maxHeap.size() == minHeap.size()) {
            // 大小相等说明有偶数个元素，堆顶相加除2
            return (double) (maxHeap.peek() + minHeap.peek()) / 2;
        } else {
            // 奇数的话，中位数是元素个数多的堆的堆顶
            return maxHeap.size() > minHeap.size() ? maxHeap.peek() : minHeap.peek();
        }
    }
    
    // 保证大根堆和小根堆内的元素个数相差不超过1个
    private void balance() {
        // 相差超过1个
        if (Math.abs(maxHeap.size() - minHeap.size()) == 2) {
            // 谁的元素多，谁就弹出一个然后扔给对方
            if (maxHeap.size() > minHeap.size()) {
                minHeap.add(maxHeap.poll());
            } else {
                maxHeap.add(minHeap.poll());
            }
        }
    }
    
}

```

---

> ***last change: 2023/4/18***

---
