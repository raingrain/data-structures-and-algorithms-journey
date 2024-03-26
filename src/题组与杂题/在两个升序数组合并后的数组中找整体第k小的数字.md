# 在两个升序数组合并后的数组中找整体第k小的数字

## [4. 寻找两个正序数组的中位数](https://leetcode.cn/problems/median-of-two-sorted-arrays/)

> - ***Question***
>   - 给定两个大小分别为 `m` 和 `n` 的正序（从小到大）数组 `nums1` 和 `nums2` 。请你找出并返回这两个正序数组的中位数。
>   - 即对于合并后的数组，如果合并后的长度为奇数，就返回合并后的数组中间位置的数，如果合并后的长度为奇数，就返回合并后的数组的上中位数与下中位数的平均值。
>   - ***tips:***
>     - `nums1.length == m`
>     - `nums2.length == n`
>     - `0 <= m <= 1000`
>     - `0 <= n <= 1000`
>     - `1 <= m + n <= 2000`
>     - `-10^6 <= nums1[i], nums2[i] <= 10^6`

---

## *Java*

> - ***模拟***

```java
class Solution {
    
    // 题目要求的
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        // 合并后的数组长度
        int size = nums1.length + nums2.length;
        // 合并后的数组长度是否为偶数
        boolean isEven = size % 2 == 0;
        // 两个数组都不为空
        if (nums1.length != 0 && nums2.length != 0) {
            if (isEven) {
                return (double) (findKthNum(nums1, nums2, size / 2) + findKthNum(nums1, nums2, size / 2 + 1)) / 2D;
            } else {
                return findKthNum(nums1, nums2, size / 2 + 1);
            }
        } else if (nums1.length != 0) {
            // 一个数组为空另外一个不为空
            if (isEven) {
                return (double) (nums1[(size - 1) / 2] + nums1[size / 2]) / 2;
            } else {
                return nums1[size / 2];
            }
        } else if (nums2.length != 0) {
            if (isEven) {
                return (double) (nums2[(size - 1) / 2] + nums2[size / 2]) / 2;
            } else {
                return nums2[size / 2];
            }
        } else {
            // 全为空
            return 0;
        }
    }
    
    // 算法原型
    // 下面的例子中数组列出来的值只代表位置而不代表数值
    // 给定两个升序数组，返回合并后的数组中整体第k小的数字
    // 算法的时间复杂度为O(log(Min(m, n)))
    private int findKthNum(int[] arr1, int[] arr2, int k) {
        int[] longArr = arr1.length >= arr2.length ? arr1 : arr2;
        int[] shortArr = arr1.length < arr2.length ? arr1 : arr2;
        // 下面以 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17
        // 1. 2. 3. 4. 5. 6. 7. 8. 9. 10.为例
        // 如果k小于短数组长度，直接把长数组和短数组截取出k长度，两个等长数组进模型
        if (k <= shortArr.length) {
            return getUpMedian(shortArr, 0, k - 1, longArr, 0, k - 1);
        }
        // 如果k大于长数组长度
        if (k > longArr.length) {
            // 求第23小的
            // 首先排除短数组中的1. ~ 5.
            // 然后排除长数组中的1 ~ 12
            // 剩下的数组中，单独验证6.和13.能不能够成为第23小的
            if (shortArr[k - longArr.length - 1] >= longArr[longArr.length - 1]) {
                return shortArr[k - longArr.length - 1];
            }
            if (longArr[k - shortArr.length - 1] >= shortArr[shortArr.length - 1]) {
                return longArr[k - shortArr.length - 1];
            }
            // 如果都不行，就验证
            // 14 15 16 17
            // 7. 8. 9. 10.
            // 中第四小的数字
            // 此时加上前面排除的19个数字刚好是第23个
            // 如果是不进行上面的验证，直接选
            // 13 14 15 16 17
            // 6. 7. 8. 9. 10.
            // 的第5小，会导致加上前面排除的16个数字是22个，少一个
            return getUpMedian(shortArr, k - longArr.length, shortArr.length - 1, longArr, k - shortArr.length, longArr.length - 1);
        }
        // 剩下的只能是k大于短数组长度且小于等于长数组长度
        // 如求第15小的
        // 首先排除长数组中的1 2 3 4
        // 再排除长数组中的16 17
        // 然后短数组中一个也排除不了
        // 但此时长数组比短数组多一个，去验证一下5能不能成为第15个
        if (longArr[k - shortArr.length - 1] >= shortArr[shortArr.length - 1]) {
            return longArr[k - shortArr.length - 1];
        }
        return getUpMedian(shortArr, 0, shortArr.length - 1, longArr, k - shortArr.length, k - 1);
    }
    
    // 算法原型
    // 给定两个等长的有序升序数组，求它们合并后的上中位数，由于合并后长度为偶数，假设长度为10就是求第5小
    private int getUpMedian(int[] arr1, int left1, int right1, int[] arr2, int left2, int right2) {
        // 当两个数组都排除掉只剩下1个的时候，放在外面讨论
        while (left1 < right1) {
            int mid1 = left1 + (right1 - left1) / 2;
            int mid2 = left2 + (right2 - left2) / 2;
            if (arr1[mid1] == arr2[mid2]) {
                // 首先如果两个的中位数相等，那就一定会填上所需位置
                // 1 2 3 4
                // 1. 2. 3. 4.
                // 如果2 == 2. 那么2和2.占据3和4位置
                return arr1[mid1];
            }
            if ((right1 - left1 + 1) % 2 != 0) {
                // 如果单个数组的长度是奇数
                // 1 2 3 4 5
                // 1. 2. 3. 4. 5.
                // 首先3 == 3.上面搞过了
                // 我们只看3大于3.
                // 首先345一定不可能是总体第5小
                // 其次1. 2.一定不可能是总体第5小
                // 那么只剩下1 2 3. 4. 5.
                if (arr1[mid1] > arr2[mid2]) {
                    // 我们发现两个数组不等长，没法继续下去
                    // 我们单独验证3.是不是总体第五小
                    if (arr2[mid2] >= arr1[mid1 - 1]) {
                        // 是就返回
                        return arr2[mid2];
                    }
                    // 不是排除它，变成两个等长数组
                    // 1 2
                    // 4. 5.
                    // 这两个新数组合并后的上中位数就是整体的上中位数
                    right1 = mid1 - 1;
                    left2 = mid2 + 1;
                } else {
                    // 一样的，对称代码罢了
                    if (arr1[mid1] >= arr2[mid2 - 1]) {
                        return arr1[mid1];
                    }
                    right2 = mid2 - 1;
                    left1 = mid1 + 1;
                }
            } else {
                // 如果单个数组的长度是偶数
                // 1 2 3 4
                // 1. 2. 3. 4.
                // 首先2 == 2.上面搞过了
                // 我们只看2大于2.
                // 首先34一定不可能是总体第5小
                // 其次1. 2.一定不可能是总体第5小
                // 那么只剩下1 2 3. 4.
                // 1 2
                // 3. 4.
                // 这两个新数组合并后的上中位数就是整体的上中位数
                if (arr1[mid1] > arr2[mid2]) {
                    right1 = mid1;
                    left2 = mid2 + 1;
                } else {
                    right2 = mid2;
                    left1 = mid1 + 1;
                }
            }
        }
        // 最后两个数组都只剩下一个数，求第一小
        return Math.min(arr1[left1], arr2[left2]);
    }
    
}
```

---

> ***last change: 2023/4/5***

---
