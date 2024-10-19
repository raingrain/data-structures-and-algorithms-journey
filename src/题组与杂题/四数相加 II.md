# 四数相加 II

## [454. 四数相加 II](https://leetcode.cn/problems/4sum-ii/)

> - ***Question***
>   - 给你四个整数数组 `nums1, nums2, nums3, nums4` ，数组长度都是 `n` ，请你计算有多少个元组 `(i, j, k, l)` 能满足：
>     - `0 <= i, j, k, l < n`
>     - `nums1[i] + nums2[j] + nums3[k] + nums4[l] == 0`
>   - ***tips:***
>     - `n == nums1.length`
>     - `n == nums2.length`
>     - `n == nums3.length`
>     - `n == nums4.length`
>     - `1 <= n <= 200`
>     - `-2^28 <= nums1[i], nums2[i], nums3[i], nums4[i] <= 2^28`

---

## *Java*

> - ***分治***
>   - 类似于根据数据量猜解法的分治。

```java
class Solution {
    
    public int fourSumCount(int[] nums1, int[] nums2, int[] nums3, int[] nums4) {
        // key为nums1和nums2中各选一个元素的和，value为有多少种选法的和是key
        HashMap<Integer, Integer> map = new HashMap<>();
        for (int num1 : nums1) {
            for (int num2 : nums2) {
                int sum = num1 + num2;
                map.put(sum, map.getOrDefault(sum, 0) + 1);
            }
        }
        int ans = 0;
        // 统计nums3和nums4中各选一个元素的和，看看nums1和nums2中有没有相反数即可
        for (int num3 : nums3) {
            for (int num4 : nums4) {
                ans += map.getOrDefault(-(num3 + num4), 0);
            }
        }
        return ans;
    }
    
}
```
