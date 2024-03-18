# n数之和问题

## [1. 两数之和](https://leetcode.cn/problems/two-sum/)

> - ***Question 1***
>   - 给定一个整数数组 `nums` 和一个整数目标值 `target` ，请你在该数组中找出和为目标值 `target` 的那两个整数，并返回它们的数组下标。
>   - 你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。
>   - 你可以按任意顺序返回答案。
>   - ***tips:***
>     - `2 <= nums.length <= 10^4`
>     - `-10^9 <= nums[i] <= 10^9`
>     - `-10^9 <= target <= 10^9`
>     - 只会存在一个有效答案

## [15. 三数之和](https://leetcode.cn/problems/3sum/)

## [LCR 007. 三数之和](https://leetcode.cn/problems/1fGaJU/)

> - ***Question 2***
>   - 给你一个整数数组 `nums` ，判断是否存在三元组 `[nums[i], nums[j], nums[k]]` 满足 `i != j, i != k` 且 `j != k` ，同时还满足 `nums[i] + nums[j] + nums[k] == 0` 。
>   - 请你返回所有和为 `0` 且不重复的三元组。
>   - ***tips:***
>     - `3 <= nums.length <= 3000`
>     - `-10^5 <= nums[i] <= 10^5`

---

## *Java*

> - ***Question 1: 哈希表***
>   - 这样我们创建一个哈希表，对于每一个 `x` ，我们首先查询哈希表中是否存在 `target - x` ，然后将 `x` 插入到哈希表中，即可保证不会让 `x` 和自己匹配。
>   - 如果哈希表中有 `target - x` ，返回索引结果即可。

```java
class Solution {
    
    public int[] twoSum(int[] nums, int target) {
        HashMap<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            if (map.containsKey(target - nums[i])) {
                return new int[]{map.get(target - nums[i]), i};
            }
            map.put(nums[i], i);
        }
        return new int[]{-1, -1};
    }
    
}
```

> - ***Question 2: 双指针 + 两数之和***
>   - 注意这个两数之和不是 `Question 1` 的。

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

> ***last change: 2024/3/18***

---
