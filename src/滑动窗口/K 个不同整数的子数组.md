# K 个不同整数的子数组

## [992. K 个不同整数的子数组](https://leetcode.cn/problems/subarrays-with-k-different-integers/)

> - ***Question***
>   - 给定一个正整数数组 `nums` 和一个整数 `k` ，返回 `num` 中好子数组的数目。
>   - 如果 `nums` 的某个子数组中不同整数的个数恰好为 `k` ，则称 `nums` 的这个连续、不一定不同的子数组为好子数组。
>   - 例如 `[1, 2, 3, 1, 2]` 中有 `3` 个不同的整数 `1, 2, 3` 。
>   - 子数组是数组的连续部分。
>   - ***tips:***
>     - `1 <= nums.length <= 2 * 10^4`
>     - `1 <= nums[i], k <= nums.length`

---

## *Java*

> - ***两个滑动窗口***

```java
class Solution {
    
    // 维护两个窗口，它们结尾位置一样，一个代表窗口内字符种类有k种，一个代表窗口内字符种类有k-1种（即左边界往左扩字符种类增加）
    // k种一定比k-1种长，k种的左到k-1种的左的区间内一定字符相同
    // 以这个区间为起点，右边界为终点就是字符种类为k种的子数组，即以右边界作为结尾的字符种类为k个的子数组个数等于区间长度
    public int subarraysWithKDistinct(int[] nums, int k) {
        // k种数的窗口词频统计
        HashMap<Integer, Integer> kCount = new HashMap<>();
        // k-1种数的窗口词频统计
        HashMap<Integer, Integer> kMinusOneCount = new HashMap<>();
        // k种数的窗口左边界
        int kLeft = 0;
        // k-1种数的窗口左边界
        int kMinusOneLeft = 0;
        // k种数的窗口内的字符种数
        int kKinds = 0;
        // k-1种数的窗口内的字符种数
        int kMinusOneKinds = 0;
        int ans = 0;
        for (int num : nums) {
            // 新来的是新的
            if (kMinusOneCount.getOrDefault(num, 0) == 0) {
                kMinusOneKinds++;
            }
            if (kCount.getOrDefault(num, 0) == 0) {
                kKinds++;
            }
            // 更新词频表
            kMinusOneCount.put(num, kMinusOneCount.getOrDefault(num, 0) + 1);
            kCount.put(num, kCount.getOrDefault(num, 0) + 1);
            // 种类超过了，移动左边界
            while (kMinusOneKinds >= k) {
                if (kMinusOneCount.get(nums[kMinusOneLeft]) == 1) {
                    // 窗口内只有这个位置有这个字符
                    kMinusOneKinds--;
                }
                // 更新
                kMinusOneCount.put(nums[kMinusOneLeft], kMinusOneCount.get(nums[kMinusOneLeft]) - 1);
                // 左边界右移
                kMinusOneLeft++;
            }
            while (kKinds > k) {
                if (kCount.get(nums[kLeft]) == 1) {
                    kKinds--;
                }
                kCount.put(nums[kLeft], kCount.get(nums[kLeft]) - 1);
                kLeft++;
            }
            // 加答案
            ans += kMinusOneLeft - kLeft;
        }
        return ans;
    }
    
}
```

> - ***一个滑动窗口***

```java
class Solution {
    
    public int subarraysWithKDistinct(int[] nums, int k) {
        // 小于等于k的子数组个数减去小于等于k-1的子数组个数就是答案
        return subArrayWordKindLessThenOrEqualK(nums, k) - subArrayWordKindLessThenOrEqualK(nums, k - 1);
    }
    
    // 返回nums中有多少个子数组中的字符种类小于等于k
    private int subArrayWordKindLessThenOrEqualK(int[] nums, int k) {
        int left = 0, ans = 0;
        // 词频表
        HashMap<Integer, Integer> wordCount = new HashMap<>();
        // 查看以i位置结尾的子数组有多少个字符种类为k
        for (int right = 0; right < nums.length; ++right) {
            if (wordCount.getOrDefault(nums[right], 0) == 0) {
                // 新进来的是新词频，k--，k大于等于0说明字符种类小于等于k
                k--;
            }
            // 更新记录
            wordCount.put(nums[right], wordCount.getOrDefault(nums[right], 0) + 1);
            // 窗口内字符种类超过k个
            while (k < 0) {
                // 不断缩小左边界，并移除左边界数字
                wordCount.put(nums[left], wordCount.get(nums[left]) - 1);
                if (wordCount.get(nums[left]) == 0) {
                    // 左边界字符移除后窗口内没有左边界字符，字符种类-1
                    k++;
                }
                left++;
            }
            ans += right - left + 1;
        }
        return ans;
    }
    
}
```

---

> ***last change: 2023/4/25***

---
