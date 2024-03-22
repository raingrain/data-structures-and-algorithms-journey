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

## [167. 两数之和 II - 输入有序数组](https://leetcode.cn/problems/two-sum-ii-input-array-is-sorted/)

> - ***Question 3***
>   - 给你一个下标从 `1` 开始的整数数组 `numbers` ，该数组已按非递减顺序排列，请你从数组中找出满足相加之和等于目标数 `target` 的两个数。如果设这两个数分别是 `numbers[index1]` 和 `numbers[index2]` ，则 `1 <= index1 < index2 <= numbers.length` 。
>   - 以长度为 `2` 的整数数组 `[index1, index2]` 的形式返回这两个整数的下标 `index1` 和 `index2` 。
>   - 你可以假设每个输入只对应唯一的答案，而且你不可以重复使用相同的元素。
>   - 你所设计的解决方案必须只使用常量级的额外空间。
>   - ***tips:***
>     - `2 <= numbers.length <= 3 * 10^4`
>     - `-1000 <= numbers[i] <= 1000`
>     - `numbers` 按非递减顺序排列
>     - `-1000 <= target <= 1000`
>     - 仅存在一个有效答案

## [LCR 006. 两数之和 II - 输入有序数组](https://leetcode.cn/problems/kLl5u1/)

> - ***Question 4***
>   - 给定一个已按照升序排列的整数数组 `numbers` ，请你从数组中找出两个数满足相加之和等于目标数 `target` 。
>   - 函数应该以长度为 `2` 的整数数组的形式返回这两个数的下标值。 `numbers` 的下标从 `0` 开始计数 ，所以答案数组应当满足 `0 <= answer[0] < answer[1] < numbers.length` 。
>   - 假设数组中存在且只存在一对符合条件的数字，同时一个数字不能使用两次。
>   - ***tips:***
>     - `2 <= numbers.length <= 3 * 10^4`
>     - `-1000 <= numbers[i] <= 1000`
>     - `numbers` 按非递减顺序排列
>     - `-1000 <= target <= 1000`
>     - 仅存在一个有效答案

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

> - ***Question 3 & Question 4: 双指针***
>   - 初始时两个指针分别指向第一个元素位置和最后一个元素的位置。每次计算两个指针指向的两个元素之和，并和目标值比较。如果两个元素之和等于目标值，则发现了唯一解。如果两个元素之和小于目标值，则将左侧指针右移一位。如果两个元素之和大于目标值，则将右侧指针左移一位。移动指针之后，重复上述操作，直到找到答案。
>   - 使用双指针的实质是缩小查找范围。那么会不会把可能的解过滤掉？答案是不会。假设 `numbers[i] + numbers[j] = target` 是唯一解，其中 `0 <= i < j <= numbers.length - 1` 。初始时两个指针分别指向下标 `0` 和下标 `numbers.length - 1` ，左指针指向的下标小于或等于 `i` ，右指针指向的下标大于或等于 `j` 。除非初始时左指针和右指针已经位于下标 `i` 和 `j` ，否则一定是左指针先到达下标 `i` 的位置或者右指针先到达下标 `j` 的位置。
>   - 如果左指针先到达下标 `i` 的位置，此时右指针还在下标 `j` 的右侧， `sum > target` ，因此一定是右指针左移，左指针不可能移到 `i` 的右侧。
>   - 如果右指针先到达下标 `j` 的位置，此时左指针还在下标 `i` 的左侧， `sum < target` ，因此一定是左指针右移，右指针不可能移到 `j` 的左侧。
>   - 由此可见，在整个移动过程中，左指针不可能移到 `i` 的右侧，右指针不可能移到 `j` 的左侧，因此不会把可能的解过滤掉。由于题目确保有唯一的答案，因此使用双指针一定可以找到答案。
>   - 时间复杂度 `O(n)` ，其中 `n` 是数组的长度。两个指针移动的总次数最多为 `n` 次。空间复杂度 `O(1)` 。

```java
class Solution {

    public int[] twoSum(int[] numbers, int target) {
        int left = 0, right = numbers.length - 1;
        while (left < right) {
            int sum = numbers[left] + numbers[right];
            if (sum == target) {
                // Question 3
                // return new int[]{left + 1, right + 1};
                // Question 4
                return new int[]{left, right};
            } else if (sum < target) {
                left++;
            } else {
                right--;
            }
        }
        return new int[]{-1, -1};
    }

}
```

---

> ***last change: 2024/3/22***

---
