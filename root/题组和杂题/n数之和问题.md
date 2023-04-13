# n数之和问题

## [15. 三数之和](https://leetcode.cn/problems/3sum/)

> - ***Question 1***
>   - 给你一个整数数组 `nums` ，判断是否存在三元组 `[nums[i], nums[j], nums[k]]` 满足 `i != j, i != k` 且 `j != k` ，同时还满足 `nums[i] + nums[j] + nums[k] == 0` 。
>   - 请你返回所有和为 `0` 且不重复的三元组。
>   - ***tips:***
>     - `3 <= nums.length <= 3000`
>     - `-10^5 <= nums[i] <= 10^5`

---

## *Java*

> - ***Question 1: 双指针 + 两数之和***

```java
class Solution {
    
    public List<List<Integer>> threeSum(int[] nums) {
        // 先排序
        Arrays.sort(nums);
        List<List<Integer>> ans = new ArrayList<>();
        for (int i = nums.length - 1; i > 1; i--) {
            // 三元组最后一个数，是arr[i]   之前....二元组 + arr[i]
            if (i == nums.length - 1 || nums[i] != nums[i + 1]) {
                List<List<Integer>> twoSumAns = twoSum(nums, i - 1, -nums[i]);
                for (List<Integer> cur : twoSumAns) {
                    // 三元组从后面加，ArrayList效率高
                    cur.add(nums[i]);
                    ans.add(cur);
                }
            }
        }
        return ans;
    }
    
    // nums[0...end]这个范围上，有多少个不同二元组，相加==target，全返回
    // {-1,5}     K = 4
    // {1, 3}
    public List<List<Integer>> twoSum(int[] nums, int end, int target) {
        int left = 0;
        int right = end;
        List<List<Integer>> ans = new ArrayList<>();
        // 双指针从两边往中间遍历
        while (left < right) {
            if (nums[left] + nums[right] > target) {
                // 大于右指针左移
                right--;
            } else if (nums[left] + nums[right] < target) {
                // 小于左指针右移
                left++;
            } else {
                // 开头或者和前一个位置不一样
                if (left == 0 || nums[left - 1] != nums[left]) {
                    List<Integer> cur = new ArrayList<>();
                    cur.add(nums[left]);
                    cur.add(nums[right]);
                    ans.add(cur);
                }
                left++;
            }
        }
        return ans;
    }
    
}
```

---

> ***last change: 2023/4/13***

---
