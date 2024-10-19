# 和至少为 K 的最短子数组

## [862. 和至少为 K 的最短子数组](https://leetcode.cn/problems/shortest-subarray-with-sum-at-least-k/)

> - ***Question 1***
>   - 给你一个整数数组 `nums` 和一个整数 `k` ，找出 `nums` 中和至少为 `k` 的最短非空子数组，并返回该子数组的长度。如果不存在这样的子数组 ，返回 `-1` 。
>   - ***tips:***
>     - `1 <= nums.length <= 10^5`
>     - `-10^5 <= nums[i] <= 10^5`
>     - `1 <= k <= 10^9`

## [209. 长度最小的子数组](https://leetcode.cn/problems/minimum-size-subarray-sum/)

## [剑指 Offer II 008. 和大于等于 target 的最短子数组](https://leetcode.cn/problems/2VG8Kg/)

> - ***Question 2***
>   - `Question 1` 中的数组改为正整数数组。
>   - ***tips:***
>     - `1 <= target <= 10^9`
>     - `1 <= nums.length <= 10^5`
>     - `1 <= nums[i] <= 10^5`

---

## *Java*

> - ***Question 1 & Question 2: 单调双端队列***
>   - `Question 1` 是通解，满足数组中是任意数值的情况。本质上是前缀和与二分查找的优化，直接一次遍历就搞定了。

```java
class Solution {

    public int shortestSubarray(int[] nums, int k) {
        // 搞出前缀和nums[i ~ j] = prefix[j + 1] - prefixSum[i]
        // 考虑以每个位置结尾的子数组，往左边扩张，最短的大于等于K的非空子数长度是几
        // nums[j ~ i]要大于等于k
        // 即nums[0 ~ j - 1]小于等于nums[0 ~ i] - nums[j ~ i]，且j要尽量靠右
        // 这里可以使用二分查找，但不够优秀
        long[] prefixSum = new long[nums.length + 1];
        for (int i = 0; i < nums.length; i++) {
            prefixSum[i + 1] = nums[i] + prefixSum[i];
        }
        int ans = Integer.MAX_VALUE;
        // 数组模拟队列，存储前缀和位置
        // 前缀和从头到尾单调递增
        // 前缀位置0-i的i也单调递增
        int[] deque = new int[nums.length + 1];
        int head = 0;
        int tail = 0;
        for (int i = 0; i < nums.length + 1; i++) {
            // 300 400 600 900 1300
            // 0-5 0-6 0-7 0-8 0-13
            // 现在是17位置，我现在要找一个小于等于800的，那我选中600
            // 假设来到了18位置，达标的是300，那又如何，6-18比8-17长！事实上我们并没有使用二分进行查找，而是一边找一边弹出，你比我长，就算我知道了你的位置的答案，但我队列中没有了，我也不拿出来比较，因为你根本影响不了答案！直接删除即可
            while (head != tail && prefixSum[i] - prefixSum[deque[head]] >= k) {
                ans = Math.min(ans, i - deque[head++]);
            }
            // 尾部开始，前缀和比当前的前缀和大于等于的，从尾部弹出！
            // 当前前缀比之前前缀小，还比之前前缀长
            // 比如0-6前缀600，0-7前缀500准备进入，我意图找前缀小于等于某个值的最右的位置，要不两个都不达标，要不选0-7，怎么都不会选0-6，因为它不能让后面够长
            while (head != tail && prefixSum[deque[tail - 1]] >= prefixSum[i]) {
                tail--;
            }
            // 入队
            deque[tail++] = i;
        }
        return ans != Integer.MAX_VALUE ? ans : -1;
    }

    public int minSubArrayLen(int target, int[] nums) {
        int ans = shortestSubarray(nums, target);
        return ans == -1 ? 0 : ans;
    }

}
```

> - ***Question 2: 滑动窗口***
>   - 时间复杂度 `O(n)` ，空间复杂度 `O(1)` 。

```java
class Solution {

    public int minSubArrayLen(int target, int[] nums) {
        int ans = Integer.MAX_VALUE;
        // 窗口左右边界
        int left = 0;
        int right = 0;
        // 窗口内元素和
        int sum = 0;
        while (right < nums.length) {
            // 右边界的进来
            sum += nums[right];
            while (sum >= target) {
                // 当前窗口内元素和大于等于目标值，比较答案
                ans = Math.min(ans, right - left + 1);
                // 左边界前进
                sum -= nums[left];
                left++;
            }
            // 右边界扩张
            right++;
        }
        return ans == Integer.MAX_VALUE ? 0 : ans;
    }

}
```

---

> ***last change: 2023/5/7***

---
