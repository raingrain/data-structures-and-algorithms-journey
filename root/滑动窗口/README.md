# 滑动窗口内最大值与最小值的更新结构

## [239. 滑动窗口最大值](https://leetcode.cn/problems/sliding-window-maximum/)

## [剑指 Offer 59 - I. 滑动窗口的最大值](https://leetcode.cn/problems/hua-dong-chuang-kou-de-zui-da-zhi-lcof/)

> - ***Question***
>   - 给你一个整数数组 `nums` ，有一个大小为 `k` 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 `k`个数字。滑动窗口每次只向右移动一位。返回滑动窗口中的最大值。
>   - ***tips:***
>     - `1 <= nums.length <= 10^5`
>     - `-10^4 <= nums[i] <= 10^4`
>     - `1 <= k <= nums.length`

---

## *Java*

> - ***双端队列法***
>   - 双端队列的含义，如果此时开始缩小窗口，哪些数组会依次成为最值。
>   - 遍历数组，并用 `left` 和 `right` 来标记窗口的左边界和右边界。队列中保存的是数值对应的数组下标位置，并且队列中索引对应的数组中的数要从大到小排序。如果当前遍历的数大于等于队尾索引对应的数，则需要弹出队尾，直到队列重新满足从大到小的要求。刚开始遍历时， `left` 和 `right` 都为 `0` ，有一个形成窗口的过程，此过程没有最大值， `left` 不动， `right` 向右移。当窗口大小形成时， `left` 和 `right` 一起向右移，每次移动时判断队首的值的数组下标是否在 `[left, right]` 中，如果不在则需要弹出队首的值，当前窗口的最大值即为队首的数。
>   - 首先我们的双端队列需要自己处理窗口长度，我们采用检查过期元素的方式省去了对 `left` 的限制，只专注于 `right` 。
>   - 由于题目中窗口长度固定为 `k` ，不同于暴力方法中最开始的窗口是从 `0` 到 `k-1` ，我们通过判断什么时候往 `ans` 中填答案来解决最开始队列不完整的问题（双端队列法从没有值开始扩张窗口），即只有当 `right >= k - 1` 时才开始往 `ans` 中填答案。
>   - 如果是求最小值的话，只需要把队列中索引对应数字的顺序变成从头至尾严格由小到大排序即可。
>   - 时间复杂度： `O(n)` ，其中 `n` 是数组 `nums` 的长度。每一个下标恰好被放入队列一次，并且最多被弹出队列一次，因此时间复杂度为 `O(n)` 。
>   - 空间复杂度： `O(k)` 。双端队列不断从队首弹出元素保证了队列中最多不会有超过 `k + 1` 个元素，因此队列使用的空间为 `O(k)` 。
> - ***大根堆***
>   - 存入一个二元组 `[index, nums[index]]` ，每次窗口移动都要加入元素并弹出过期元素，堆内按照 `nums[index]` 降序排序，基本逻辑和双端队列相同，结构换了一下而已。
>   - 时间复杂度： `O(nlogn)` ，其中 `n` 是数组 `nums` 的长度。在最坏情况下，数组 `nums` 中的元素单调递增，那么最终优先队列中包含了所有元素，没有元素被移除。由于将一个元素放入优先队列的时间复杂度为 `O(logn)` ，因此总时间复杂度为 `O(n \logn)` 。
>   - 空间复杂度： `O(n)` ，即为优先队列需要使用的空间。这里所有的空间复杂度分析都不考虑返回的答案需要的 `O(n)` 空间，只计算额外的空间使用。

```java
class Solution {
    
    public int[] maxSlidingWindow(int[] nums, int k) {
        // return maxSlidingWindowByDeque(nums, k);
        return maxSlidingWindowByPriorityQueue(nums, k);
    }
    
    // 双端队列
    public int[] maxSlidingWindowByDeque(int[] nums, int k) {
        /*if (nums == null || k < 1 || nums.length < k) {
            return null;
        }*/
        // 队列中存储的是下标，因为要移除窗口移动后过期的元素
        // 存值的话没法进行判断
        // 双端队列
        Deque<Integer> maxDeque = new ArrayDeque<>();
        // 结果数组，由题意其长度确定
        int[] ans = new int[nums.length - k + 1];
        // 遍历ans要的
        int index = 0;
        for (int right = 0; right < nums.length; ++right) {
            // 当队列里面有值且nums[队尾元素]小于等于nums[right]，弹出队尾元素
            while (!maxDeque.isEmpty() && nums[maxDeque.getLast()] <= nums[right]) {
                maxDeque.pollLast();
            }
            // 先抛出队列中所有小于等于nums[right]的元素下标再加入
            maxDeque.offerLast(right);
            // 检查队首元素是不是过期元素，是的话就弹出队列
            if (maxDeque.getFirst() == right - k) {
                maxDeque.pollFirst();
            }
            // right >= k - 1是窗口在数组中完整出现的条件
            // 此时可以开始往ans中填答案
            if (right >= k - 1) {
                ans[index++] = nums[maxDeque.getFirst()];
            }
        }
        return ans;
    }
    
    // 大根堆
    private int[] maxSlidingWindowByPriorityQueue(int[] nums, int k) {
        PriorityQueue<int[]> heap = new PriorityQueue<>((o1, o2) -> o2[1] - o1[1]);
        int[] ans = new int[nums.length - k + 1];
        int index = 0;
        for (int right = 0; right < nums.length; ++right) {
            heap.offer(new int[]{right, nums[right]});
            if (right >= k - 1) {
                while (heap.peek()[0] <= right - k) {
                    heap.poll();
                }
                ans[index++] = heap.peek()[1];
            }
        }
        return ans;
    }
    
}
```

---

> ***last change: 2023/4/19***

---
