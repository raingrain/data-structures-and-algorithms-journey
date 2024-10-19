# 找出第 K 小的数对距离

## [719. 找出第 K 小的数对距离](https://leetcode.cn/problems/find-k-th-smallest-pair-distance/)

> - ***Question***
>   - 数对 `(a, b)` 由整数 `a` 和 `b` 组成，其数对距离定义为 `a` 和 `b` 的绝对差值。
>   - 给你一个整数数组 `nums` 和一个整数 `k` ，数对由 `nums[i]` 和 `nums[j]` 组成且满足 `0 <= i < j < nums.length` 。返回所有数对距离中第 `k` 小的数对距离。
>   - ***tips:***
>     - `n == nums.length`
>     - `2 <= n <= 10^4`
>     - `0 <= nums[i] <= 10^6`
>     - `1 <= k <= n * (n - 1) / 2`

---

## *Java*

> - ***排序 + 二分查找 + 双指针***
>   - 先将数组 `nums` 从小到大进行排序。因为第 `k` 小的数对距离必然在区间 `[0, max(nums) - min(nums)]` 内，令 `left = 0` ， `right = max(nums) - min(nums)` ，我们在区间 `[left, right]` 上进行二分。
>   - 对于当前搜索的距离 `mid` ，计算所有距离小于等于 `mid` 的数对数目 `cnt` ，如果 `cnt >= k` ，那么 `right = mid - 1` 并记录答案，否则 `left = mid + 1` 。当 `left > right` 时，终止搜索，那么第 `k` 小的数对距离为 `left` 。
>   - 给定距离 `mid` ，计算所有距离小于等于 `mid` 的数对数目 `cnt` 可以使用双指针：初始左端点 `i = 0` ，我们从小到大枚举所有数对的右端点 `j` ，移动左端点直到 `nums[j] - nums[i] <= mid` ，那么右端点为 `j` 且距离小于等于 `mid` 的数对数目为 `j - i` ，计算这些数目之和。

```java
class Solution {

    public int smallestDistancePair(int[] nums, int k) {
        Arrays.sort(nums);
        int left = 0;
        int right = nums[nums.length - 1];
        int ans = 0;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            int cnt = lessThanOrEqualDiatance(nums, mid);
            if (cnt >= k) {
                ans = mid;
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        return ans;
    }

    private int lessThanOrEqualDiatance(int[] arr, int distance) {
        int ans = 0;
        for (int left = 0, right = 0; left < arr.length; left++) {
            while (right < arr.length && arr[right] - arr[left] <= distance) {
                right++;
            }
            ans += right - left - 1;
        }
        return ans;
    }

}
```
