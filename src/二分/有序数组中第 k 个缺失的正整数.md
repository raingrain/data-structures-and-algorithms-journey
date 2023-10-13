# 第 k 个缺失的正整数

## [1539. 第 k 个缺失的正整数](https://leetcode.cn/problems/kth-missing-positive-number/)

> - ***Question***
>   - 给你一个严格升序排列的正整数数组 `arr` 和一个整数 `k` 。请你找到这个数组里第 `k` 个缺失的正整数。
>   - ***tips:***
>     - `1 <= arr.length <= 1000`
>     - `1 <= arr[i] <= 1000`
>     - `1 <= k <= 1000`
>     - 对于所有 `1 <= i < j <= arr.length` 的 `i` 和 `j` 满足 `arr[i] < arr[j]`

---

## *Java*

> - ***二分查找***

```java
class Solution {

    public int findKthPositive(int[] arr, int k) {
        int l = 0;
        int r = arr.length - 1;
        // 0 ~ n-1
        int m = 0;
        // 对号的位置！find！
        // 到arr[find]为止缺失的元素数量pi>=k
        // 到arr[find-1]为止缺失的元素数量pi<k
        int find = arr.length;
        while (l <= r) {
            // 中点下标，m
            m = (l + r) / 2;
            // arr[m] - (m + 1)
            if (arr[m] - (m + 1) >= k) {
                // 缺失的第k个正整数
                // 在 1 ~ arr[m]
                // 范围能不能继续变小，去左侧二分
                // l ..... m  ..... r
                // l     r
                find = m;
                r = m - 1;
            } else {
                // 缺失的第k个正整数
                // 不在 1 ~ arr[m]范围上，不够
                l = m + 1;
            }
        }
        //      x 100
        //     16 17(find)
        // find
        // 0
        //                x
        //  0 1 2 3 4 5 6 7 8(find)
        // 如果find是0，上一个元素设置为0
        // 如果是越界，find保持length不变，pre是最后一个元素
        int preValue = find == 0 ? 0 : arr[find - 1];
        // under是到arr[find-1]为止缺失的元素数量pi
        int under = preValue - find;
        // k - under是从arr[find-1]开始还缺几个，加上即可
        return preValue + (k - under);
    }

}
```

---

> ***last change: 2023/5/18***

---
