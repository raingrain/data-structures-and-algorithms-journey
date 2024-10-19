# 找到 K 个最接近的元素

## [658. 找到 K 个最接近的元素](https://leetcode.cn/problems/find-k-closest-elements/)

> - ***Question***
>   - 给定一个排序好的数组 `arr` ，两个整数 `k` 和 `x` ，从数组中找到最靠近 `x` （两数之差最小）的 `k` 个数。返回的结果必须要是按升序排好的。
>   - 整数 `a` 比整数 `b` 更接近 `x` 需要满足： `|a - x| < |b - x|` 或者 `|a - x| == |b - x|` 且 `a < b` 。
>   - ***tips:***
>     - `1 <= k <= arr.length`
>     - `1 <= arr.length <= 10^4`
>     - `arr` 按升序排列
>     - `-10^4 <= arr[i], x <= 10^4`

---

## *Java*

> - ***二分查找 + 双指针***
>   - 假设数组长度为 `n` ，注意到数组 `arr` 已经按照升序排序，我们可以将数组 `arr` 分成两部分，前一部分所有元素 `[0, left]` 都小于 `x` ，后一部分所有元素 `[right, n - 1]` 都大于等于 `x` ， `left` 与 `right` 都可以通过二分查找获得。
>   - `left` 和 `right` 指向的元素都是各自部分最接近 `x` 的元素，因此我们可以通过比较 `left` 和 `right` 指向的元素获取整体最接近 `x` 的元素。如果 `x - arr[left] <= arr[right] - x` ，那么将 `left` 减一，否则将 `right` 加一。相应地，如果 `left` 或 `right` 已经越界，那么不考虑对应部分的元素。
>   - 最后，区间 `[left + 1,right - 1]` 的元素就是我们所要获得的结果，返回答案既可。

```java
import java.util.*;

class Solution {

    public List<Integer> findClosestElements(int[] arr, int k, int x) {
        int right = binarySearch(arr, x);
        int left = right - 1;
        while (k-- > 0) {
            if (left < 0) {
                right++;
            } else if (right >= arr.length) {
                left--;
            } else if (x - arr[left] <= arr[right] - x) {
                left--;
            } else {
                right++;
            }
        }
        List<Integer> ans = new ArrayList<>();
        for (int i = left + 1; i < right; i++) {
            ans.add(arr[i]);
        }
        return ans;
    }

    public int binarySearch(int[] arr, int x) {
        int low = 0, high = arr.length - 1;
        while (low < high) {
            int mid = low + (high - low) / 2;
            if (arr[mid] >= x) {
                high = mid;
            } else {
                low = mid + 1;
            }
        }
        return low;
    }

}

```
