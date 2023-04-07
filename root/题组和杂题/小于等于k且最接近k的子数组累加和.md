# 小于等于k且最接近k的子数组累加和

## 左老师课堂讲述

> - ***Question***
>   - 给定一个数组 `arr` 和一个值 `k` ，返回小于等于 `k` 且最接近 `k` 的子数组累加和。

---

## *Java*

> - ***前缀和 + 有序表***
>   - 分别讨论以每个位置为结尾的子数组的累加和，看看以谁结尾的子数组累加和最大且小于等于 `k` 。
>   - 对于以 `i` 位置结尾的子数组，我们有 `arr[0 ~ i]` 的总和，又有 `arr[0 ~ j] (j < i)` 的前缀和，要想一个以 `j + 1` 位置作为开头位置的子数组的累加和小于等于 `k` ，就要使得 `arr[0 ~ j]` 的累加和大于等于 `k` 且最小。这可以i通过将前缀和放入有序表中调用库函数快速实现。

```java
class Solution {
    
    public int maxSubArraySumLessOrEqualK(int[] arr, int k) {
        // 存储前缀和的有序表
        TreeSet<Integer> set = new TreeSet<>();
        // 一个数都没有，表示全部累加和
        set.add(0);
        // 记录答案
        int ans = Integer.MIN_VALUE;
        // 前缀和 
        int sum = 0;
        // 遍历数组
        for (int num : arr) {
            sum += num;
            // 返回此集合中大于或等于给定元素的最小元素，如果没有这样的元素，则返回null。
            if (set.ceiling(sum - k) != null) {
                ans = Math.max(ans, sum - set.ceiling(sum - k));
            }
            // 别忘了加上给后面用
            set.add(sum);
        }
        return ans;
    }
    
}
```

---

> ***last change: 2023/4/7***

---
