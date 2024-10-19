# 713. 乘积小于 K 的子数组

## [713. 乘积小于 K 的子数组](https://leetcode.cn/problems/subarray-product-less-than-k/)

## [LCR 009. 乘积小于 K 的子数组](https://leetcode.cn/problems/ZVAVXX/)

> - ***Question***
>   - 给你一个整数数组 nums 和一个整数 k ，请你返回子数组内所有元素的乘积严格小于 k 的连续子数组的数目。
>   - ***tips:***
>     - `1 <= nums.length <= 3 * 10^4`
>     - `1 <= nums[i] <= 1000`
>     - `0 <= k <= 10^6`

---

## *Java*

> - ***滑动窗口***
>   - 我们固定子数组 `[i, j]` 的右端点 `j` 时，显然左端点 `i` 越大，子数组元素乘积越小。对于子数组 `[i, j]` ，当左端点 `i >= l1` 时，所有子数组的元素乘积都小于 `k` ，当左端点 `i < l1` 时，所有子数组的元素乘积都大于等于 `k` 。那么对于右端点为 `j + 1` 的所有子数组，它的左端点 `i` 就不需要从 `0` 开始枚举，因为对于所有 `i < l1` 的子数组，它们的元素乘积都大于等于 `k` 。我们只要从 `i = l1` 处开始枚举，直到子数组 `i = l2` 时子数组 `[l2, j + 1]` 的元素乘积小于 `k` ，那么左端点 `i >= l2` 所有子数组的元素乘积都小于 `k` 。
>   - 根据上面的分析，我们枚举子数组的右端点 `j` ，并且左端点从 `i = 0` 开始，用 `poduct` 记录子数组 `[i, j]` 的元素乘积。每枚举一个右端点 `j` ，如果当前子数组元素乘积 `poduct` 大于等于 `k` ，那么我们右移左端点 `i` 直到满足当前子数组元素乘积小于 `k`  或者 `i > j` ，那么元素乘积小于 `k` 的子数组数目为 `j - i + 1` 。返回所有数目之和。
>   - `product` 的值始终不超过 `k x max⁡l{nums[l]}` ，因此无需担心整型溢出的问题。
>   - 时间复杂度 `O(n)` ，空间复杂度 `O(1)` 。

```java
class Solution {

    public int numSubarrayProductLessThanK(int[] nums, int k) {
        int ans = 0;
        int product = 1;
        int left = 0;
        for (int right = 0; right < nums.length; right++) {
            product *= nums[right];
            while (left <= right && product >= k) {
                product /= nums[left];
                left++;
            }
            // 以左端点为开头的连续子数组个数
            ans += right - left + 1;
        }
        return ans;
    }

}
```
