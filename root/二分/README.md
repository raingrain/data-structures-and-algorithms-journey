# 二分查找模板及其简单应用

## [704. 二分查找](https://leetcode.cn/problems/binary-search/)

## [35. 搜索插入位置](https://leetcode.cn/problems/search-insert-position/)

## [剑指 Offer II 068. 查找插入位置](https://leetcode.cn/problems/N6YdxV/)

## [34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/)

## [剑指 Offer 53 - I. 在排序数组中查找数字 I](https://leetcode.cn/problems/zai-pai-xu-shu-zu-zhong-cha-zhao-shu-zi-lcof/)

> - ***Question 1***
>   - 给定一个 `n` 个元素有序的（升序）整型数组 `nums` 和一个目标值 `target` ，写一个函数搜索 `nums` 中的 `target` ，如果目标值存在返回下标，否则返回 `-1` 。
>   - ***tips:***
>     - 你可以假设 `nums` 中的所有元素是不重复的
>     - `n` 将在 `[1, 10000]` 之间
>     - `nums` 的每个元素都将在 `[-9999, 9999]` 之间
> - ***Question 2***
>   - 给定一个排序的整数数组 `nums` 和一个整数目标值 `target` ，请在数组中找到 `target` ，并返回其下标。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。
>   - ***tips:***
>     - `1 <= nums.length <= 104`
>     - `-104 <= nums[i] <= 104`
>     - `nums` 为无重复元素的升序排列数组
>     - `-104 <= target <= 104`
> - ***Question 3***
>   - 给你一个按照非递减顺序排列的整数数组 `nums` ，和一个目标值 `target` 。请你找出给定目标值在数组中的开始位置和结束位置。如果数组中不存在目标值 `target` ，返回 `[-1, -1]` 。
>   - ***tips:***
>     - `-109 <= nums[i] <= 109`
>     - `nums` 是一个非递减数组
>     - `-109 <= target <= 109`
> - ***Question 4***
>   - 统计一个数字在排序数组中出现的次数。
>   - ***tips:***
>     - `-109 <= nums[i] <= 109`
>     - `nums` 是一个非递减数组
>     - `-109 <= target <= 109`
> - ***Question 5***
>   - 实现四个函数，分别能够返回有序数组 `nums` 中大于等于 `target` 最左、大于 `target` 最左、小于等于 `target` 最右、小于 `target` 最右的元素的索引，如果不存在这样的元素请返回 `-1` 。

---

## *Java*

> - ***二分查找***
>   - 二分查找要密切关注while循环的终止条件，左右区间选择（一律写闭区间），必要时列出情况再写代码
>   - 题目解法看注释
>   - 注意在java等需要编译的语言中，计算 `mid` 时可能出现整数运算 `left + right` 溢出，公式要写成 `mid = left + (right - left) >> 1` ，因为 `right - left` 不会溢出，除 `2` 也就不会溢出了，再加也不会溢出了；

```java
class Solution {
    
    // 二叉搜索本索
    public int search(int[] nums, int target) {
        if (nums == null || nums.length == 0) {
            return -1;
        }
        int left = 0;
        int right = nums.length - 1;
        while (left < right) {
            int mid = left + ((right - left) >> 1);
            if (nums[mid] < target) {
                left = mid + 1;
            } else if (nums[mid] > target) {
                right = mid - 1;
            } else {
                return mid;
            }
        }
        return nums[left] == target ? left : -1;
    }
    
    // 找到大于等于target最左元素的索引
    public int mostLeftNoLessNumberIndex(int[] nums, int target) {
        if (nums == null || nums.length == 0) {
            return -1;
        }
        int left = 0;
        int right = nums.length - 1;
        int ans = -1;
        while (left <= right) {
            int mid = left + ((right - left) >> 1);
            if (nums[mid] >= target) {
                ans = mid;
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        return ans;
    }
    
    // 找到大于target最左元素的索引
    public int mostLeftMoreNumberIndex(int[] nums, int target) {
        if (nums == null || nums.length == 0) {
            return -1;
        }
        int left = 0;
        int right = nums.length - 1;
        int ans = -1;
        while (left <= right) {
            int mid = left + ((right - left) >> 1);
            if (nums[mid] > target) {
                ans = mid;
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        return ans;
    }
    
    // 找到小于等于target最右元素的索引
    public int mostRightNoMoreNumberIndex(int[] nums, int target) {
        if (nums == null || nums.length == 0) {
            return -1;
        }
        int left = 0;
        int right = nums.length - 1;
        int ans = -1;
        while (left <= right) {
            int mid = left + ((right - left) >> 1);
            if (nums[mid] <= target) {
                ans = mid;
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return ans;
    }
    
    // 找到小于target最右元素的索引
    public int mostRightLessNumberIndex(int[] nums, int target) {
        if (nums == null || nums.length == 0) {
            return -1;
        }
        int left = 0;
        int right = nums.length - 1;
        int ans = -1;
        while (left <= right) {
            int mid = left + ((right - left) >> 1);
            if (nums[mid] < target) {
                ans = mid;
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return ans;
    }
    
    // 搜索一个元素的插入位置，即插入大于等于target最左元素的位置
    public int searchInsert(int[] nums, int target) {
        int ans = mostLeftNoLessNumberIndex(nums, target);
        return ans == -1 ? nums.length : ans;
    }
    
    // 搜索出现次数
    public int search(int[] nums, int target) {
        int leftIndex = mostLeftNoLessNumberIndex(nums, target);
        // 全部小于targets
        //  || nums[leftIndex] != target
        if (leftIndex == -1) {
            return 0;
        }
        // 如果不存在target但有大于target的元素存在，rightIndex - leftIndex = 0，当然也可以在上面的if中做判断
        int rightIndex = mostLeftMoreNumberIndex(nums, target);
        if (rightIndex == -1) {
            rightIndex = nums.length;
        }
        return rightIndex - leftIndex;
    }
    
    // 搜索出现范围
    public int[] searchRange(int[] nums, int target) {
        int leftIndex = mostLeftNoLessNumberIndex(nums, target);
        // 会出现数组中有大于target的元素存在，但不存在target元素，从而导致结果为[leftIndex, 0]的情况出现，因为此时leftIndex = rightIndex
        if (leftIndex == -1 || nums[leftIndex] != target) {
            return new int[]{-1, -1};
        }
        int rightIndex = mostLeftMoreNumberIndex(nums, target);
        if (rightIndex == -1) {
            rightIndex = nums.length;
        }
        return new int[]{leftIndex, rightIndex - 1};
    }
    
}
```

---

> ***last change: 2022/11/5***

---
