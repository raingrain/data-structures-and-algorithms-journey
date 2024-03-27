# 二分查找模板及其简单应用

## [704. 二分查找](https://leetcode.cn/problems/binary-search/)

> - ***Question 1***
>   - 给定一个 `n` 个元素有序的（升序）整型数组 `nums` 和一个目标值 `target` ，写一个函数搜索 `nums` 中的 `target` ，如果目标值存在返回下标，否则返回 `-1` 。
>   - ***tips:***
>     - 你可以假设 `nums` 中的所有元素是不重复的
>     - `n` 将在 `[1, 10000]` 之间
>     - `nums` 的每个元素都将在 `[-9999, 9999]` 之间

## [35. 搜索插入位置](https://leetcode.cn/problems/search-insert-position/)

## [剑指 Offer II 068. 查找插入位置](https://leetcode.cn/problems/N6YdxV/)

> - ***Question 2***
>   - 给定一个排序的整数数组 `nums` 和一个整数目标值 `target` ，请在数组中找到 `target` ，并返回其下标。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。
>   - ***tips:***
>     - `1 <= nums.length <= 10^4`
>     - `-10^4 <= nums[i] <= 10^4`
>     - `nums` 为无重复元素的升序排列数组
>     - `-10^4 <= target <= 10^4`

## [34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/)

> - ***Question 3***
>   - 给你一个按照非递减顺序排列的整数数组 `nums` ，和一个目标值 `target` 。请你找出给定目标值在数组中的开始位置和结束位置。如果数组中不存在目标值 `target` ，返回 `[-1, -1]` 。
>   - ***tips:***
>     - `-10^9 <= nums[i] <= 10^9`
>     - `nums` 是一个非递减数组
>     - `-10^9 <= target <= 10^9`

## [剑指 Offer 53 - I. 在排序数组中查找数字 I](https://leetcode.cn/problems/zai-pai-xu-shu-zu-zhong-cha-zhao-shu-zi-lcof/)

> - ***Question 4***
>   - 统计一个数字在排序数组中出现的次数。
>   - ***tips:***
>     - `-10^9 <= nums[i] <= 10^9`
>     - `nums` 是一个非递减数组
>     - `-10^9 <= target <= 10^9`

## 左老师课堂讲述

> - ***Question 5***
>   - 实现四个函数，分别能够返回有序数组 `nums` 中大于等于 `target` 最左、大于 `target` 最左、小于等于 `target` 最右、小于 `target` 最右的元素的索引，如果不存在这样的元素请返回 `-1` 。

---

## *Java*

> - ***二分查找***
>   - 二分查找要密切关注while循环的终止条件，左右区间选择（一律写闭区间），必要时列出情况再写代码。
>   - 题目解法看注释
>   - 注意在java等需要编译的语言中，计算 `mid` 时可能出现整数运算 `left + right` 溢出，公式要写成 `mid = left + (right - left) >> 1` ，因为 `right - left` 不会溢出，除 `2` 也就不会溢出了，再加也不会溢出了。

```java
class Solution {
    
    // Question 1
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
    
    // Question 2
    // 搜索一个元素的插入位置，即插入大于等于target最左元素的位置
    public int searchInsert(int[] nums, int target) {
        int ans = mostLeftNoLessNumberIndex(nums, target);
        return ans == -1 ? nums.length : ans;
    }
    
    // Question 4
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
    
    // Question 3
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

> - ***二分查找模版分析***
>   - 二分查找一般由三个主要部分组成：
>     1. 预处理：如果集合未排序，则进行排序。
>     2. 二分查找：使用循环或递归在每次比较后将查找空间划分为两半。
>     3. 后处理：在剩余空间中确定可行的候选者。
>   - 你在网上看到的 `99%` 的二分查找问题会归结于这 `3` 个模板中的一个。有些问题可以使用多个模板来实现，但是当你做更多的练习时，你会注意到一些模板比其他模板更适合某些问题。注意：模板和它们的差异已被彩色标注如下。
>   - ![image](./images/模版.png)
>   - 这 3 个模板的不同之处在于：
>     - 左、中、右索引的分配。
>     - 循环或递归终止条件。
>     - 后处理的必要性。
>   - 模板 `#1` 和 `#3` 是最常用的，几乎所有二分查找问题都可以用其中之一轻松实现。模板 `#2` 更高级一些，用于解决某些类型的问题。
>   - 这 `3` 个模板中的每一个都提供了一个特定的用例：
>     - 模板 `#1 (left <= right)` 用于查找可以通过访问数组中的单个索引来确定的元素或条件：
>       - 二分查找的最基础和最基本的形式。
>       - 查找条件可以在不与元素的两侧进行比较的情况下确定（或使用它周围的特定元素）。
>       - 不需要后处理，因为每一步中，你都在检查是否找到了元素。如果到达末尾，则知道未找到该元素。
>     - 模板 `#2 (left < right)` 用于查找需要访问数组中当前索引及其直接右邻居索引的元素或条件：
>       - 一种实现二分查找的高级方法。
>       - 查找条件需要访问元素的直接右邻居。
>       - 使用元素的右邻居来确定是否满足条件，并决定是向左还是向右。
>       - 保证查找空间在每一步中至少有 `2` 个元素。
>       - 需要进行后处理。当你剩下 `1` 个元素时，循环或递归结束。需要评估剩余元素是否符合条件。
>     - 模板 `#3 (left + 1 < right)` 用于搜索需要访问当前索引及其在数组中的直接左右邻居索引的元素或条件：
>       - 实现二分查找的另一种方法。
>       - 搜索条件需要访问元素的直接左右邻居。
>       - 使用元素的邻居来确定它是向右还是向左。
>       - 保证查找空间在每个步骤中至少有 `3` 个元素。
>       - 需要进行后处理。当剩下 `2` 个元素时，循环或递归结束。需要评估其余元素是否符合条件。
>   - 时间复杂度 `O(log n)` ，因为二分查找是通过对查找空间中间的值应用一个条件来操作的，并因此将查找空间折半，在更糟糕的情况下，我们将不得不进行 `O(log n)` 次比较，其中 `n` 是集合中元素的数目。虽然二分查找确实需要跟踪 `3` 个指标，但迭代解决方案通常不需要任何其他额外空间，并且可以直接应用于集合本身，因此需要 `O(1)` 或常量空间。

---

> ***last change: 2023/4/19***

---
