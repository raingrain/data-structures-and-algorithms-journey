# [480. 滑动窗口中位数](https://leetcode.cn/problems/sliding-window-median/)

> - 给你一个数组 nums，有一个长度为 k 的窗口从最左端滑动到最右端。窗口中有 k 个数，每次窗口向右移动 1 位
> - 请找出每次窗口移动后得到的新窗口中元素的中位数，并输出由它们组成的数组：
> - 提示：
>   - 你可以假设 k 始终有效，即：k 始终小于等于输入的非空数组的元素个数。
>   - 与真实值误差在 10 ^ -5 以内的答案将被视作正确答案。

# [295. 数据流的中位数](https://leetcode.cn/problems/find-median-from-data-stream/) & [面试题 17.20. 连续中值](https://leetcode.cn/problems/continuous-median-lcci/)

> - 设计一个支持以下两种操作的数据结构：
>   - `void addNum(int num)` - 从数据流中添加一个整数到数据结构中
>   - `double findMedian()` - 返回目前所有元素的中位数
> - 进阶:
>   - 如果数据流中所有整数都在 0 到 100 范围内，你将如何优化你的算法：
>     - 如果数据流中所有整数都在 0 到 100 范围内，那么我们可以利用计数排序统计每一类数的数量，并使用双指针维护中位数
>   - 如果数据流中 99% 的整数都在 0 到 100 范围内，你将如何优化你的算法：
>     - 如果数据流中 99% 的整数都在 0 到 100 范围内，那么我们依然利用计数排序统计每一类数的数量，并使用双指针维护中位数。对于超出范围的数，我们可以单独进行处理，建立两个数组，分别记录小于 0 的部分的数的数量和大于 100 的部分的数的数量即可。当小部分时间，中位数不落在区间 [0,100] 中时，我们在对应的数组中暴力检查即可

---

### *Java*

> - ***SB树的改写***
>   - 这种问题的特点就是，求解答案时需要的数据是范围确定且有序的，求解的过程有容易理解的暴力解但时间复杂度高。这时候我们可以利用SB树改写容易的特点，用改写的SB树来存储范围内的数据，又因为是搜索二叉树，有序也可以满足，我们只需要对数据和最终需要的解题思路改写节点、增\删方法即可，形成一个时间复杂度为O(logN)的黑盒
>   - SB树是不支持加入相同key的，如果想重复加入数字作为key（比如数组元素），我们可以用index位置作为标识，然后用一个对象把它们包装起来，当然如果只要求重复加而不需要识别，也可以像[327. 区间和的个数](https://leetcode.cn/problems/count-of-range-sum/)一样用一个数据项存储路过的节点数

```java
// 滑动窗口中位数
class Solution {
    public double[] medianSlidingWindow(int[] nums, int k) {
        SBTree<Key> map = new SBTree<>();
        // 把前k - 1个加入先
        for (int i = 0; i < k - 1; i++) {
            map.add(new Key(i, nums[i]));
        }
        double[] ans = new double[nums.length - k + 1];
        int index = 0;
        for (int i = k - 1; i < nums.length; i++) {
            // 加上这一轮的末尾
            map.add(new Key(i, nums[i]));
            // 严格意义上的中位数
            if (map.size() % 2 == 0) {
                // 上下中位数之和除以2
                Key upMidKey = map.getIndexKey(map.size() / 2 - 1);
                Key downMidKey = map.getIndexKey(map.size() / 2);
                ans[index++] = ((double) upMidKey.value + (double) downMidKey.value) / 2;
            } else {
                Key mid = map.getIndexKey(map.size() / 2);
                ans[index++] = mid.value;
            }
            // 移除这一轮的开头
            map.remove(new Key(i - k + 1, nums[i - k + 1]));
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
    private SBTree<Key> map;

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
        size = 1;
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
    @Override
    public int compareTo(Key otherKey) {
        return value != otherKey.value ? Integer.valueOf(value).compareTo(otherKey.value) : Integer.valueOf(index).compareTo(otherKey.index);
    }
}


// SB树
class SBTree<K extends Comparable<K>> {
    private SBNode<K> root;

    private int getSize(SBNode<K> head) {
        return 1 + (head.left == null ? 0 : head.left.size) + (head.right == null ? 0 : head.right.size);
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

    private SBNode<K> maintain(SBNode<K> cur) {
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
            cur.right = maintain(cur.right);
            cur = maintain(cur);
        } else if (leftRightSize > rightSize) {
            cur.left = leftRotate(cur.left);
            cur = rightRotate(cur);
            cur.left = maintain(cur.left);
            cur.right = maintain(cur.right);
            cur = maintain(cur);
        } else if (rightRightSize > leftSize) {
            cur = leftRotate(cur);
            cur.left = maintain(cur.left);
            cur = maintain(cur);
        } else if (rightLeftSize > leftSize) {
            cur.right = rightRotate(cur.right);
            cur = leftRotate(cur);
            cur.left = maintain(cur.left);
            cur.right = maintain(cur.right);
            cur = maintain(cur);
        }
        return cur;
    }

    private SBNode<K> add(SBNode<K> head, K key) {
        if (head == null) {
            return new SBNode<K>(key);
        } else {
            head.size++;
            if (key.compareTo(head.key) < 0) {
                head.left = add(head.left, key);
            } else {
                head.right = add(head.right, key);
            }
            return maintain(head);
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

    private SBNode<K> findLastIndex(K key) {
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
            SBNode<K> lastNode = findLastIndex(key);
            return lastNode != null && key.compareTo(lastNode.key) == 0;
        }
    }

    public void add(K key) {
        if (key == null) {
            throw new RuntimeException("invalid parameter.");
        } else {
            SBNode<K> lastNode = findLastIndex(key);
            if (lastNode == null || key.compareTo(lastNode.key) != 0) {
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

    public K getIndexKey(int index) {
        if (index < 0 || index >= this.size()) {
            throw new RuntimeException("invalid parameter.");
        } else {
            return getIndex(root, index + 1).key;
        }
    }
}
```
