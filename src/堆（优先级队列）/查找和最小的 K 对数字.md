# 查找和最小的 K 对数字

## [373. 查找和最小的 K 对数字](https://leetcode.cn/problems/find-k-pairs-with-smallest-sums/)

## [LCR 061. 查找和最小的 K 对数字](https://leetcode.cn/problems/qn8gGX/)

> - ***Question***
>   - 给定两个以升序排列的整数数组 `nums1` 和 `nums2` ，以及一个整数 `k` 。
>   - 定义一对值 `(u, v)` ，其中第一个元素来自 `nums1` ，第二个元素来自 `nums2` 。
>   - 请找到和最小的 `k` 个数对 `(u1, v1), (u2, v2), ..., (uk, vk)` 。
>   - ***tips:***
>     - `1 <= nums1.length, nums2.length <= 10^5`
>     - `-10^9 <= nums1[i], nums2[i] <= 10^9`
>     - `nums1` 和 `nums2` 均为升序排列
>     - `1 <= k <= 10^4`
>     - `k <= nums1.length * nums2.length`

## Java

> - ***小根堆***
>   - 对于已经按升序排列的两个数组 `nums1, nums2` ，长度分别为 `length1, length2` ，我们可以知道和最小的数对一定为 `(nums1[0], nums2[0])` ，和最大的数对一定为 `(nums1[length1 - 1], nums2[length2 - 1])` 。本题要求找到最小的 `k` 个数对，最直接的办法是可以将所有的数对求出来，然后利用排序或者 `TopK` 解法求出最小的 `k` 个数对即可。实际求解时可以不用求出所有的数对，只需从所有符合待选的数对中选出最小的即可，假设当前已选的前 `n` 小数对的索引分别为 `(a1, b1), (a2, b2), (a3, b3), ..., (an, bn)` ，由于两个数组都是按照升序进行排序的，则可以推出第 `n + 1` 小的数对的索引选择范围为 `(a1 + 1, b1), (a1, b1 + 1), (a2 + 1, b2), (a2, b2 + 1), (a3 + 1, b3), (a3, b3 + 1), ..., (an + 1, bn), (an, bn + 1)` ，假设我们利用堆的特性可以求出待选范围中最小数对的索引为 `(ai, bi)` 加入到堆中，直到我们选出 `k` 个数对即可。
>   - 如果我们每次都将已选的数对 `(ai, bi)` 的待选索引 `(ai+1, bi), (ai, bi+1)` 加入到堆中则可能出现重复的问题，一般需要设置标记位解决去重的问题。我们可以将 `nums1` 的前 `k` 个索引数对 `(0, 0), (1, 0), ...,(k - 1, 0)` 加入到队列中，每次从队列中取出元素 `(x, y)` 时，我们只需要将 `nums2` 的索引增加即可，这样避免了重复加入元素的问题。

```java
class Solution {

    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        PriorityQueue<int[]> pq = new PriorityQueue<>(k, (o1, o2) -> nums1[o1[0]] + nums2[o1[1]] - nums1[o2[0]] - nums2[o2[1]]);
        List<List<Integer>> ans = new ArrayList<>();
        int m = nums1.length;
        int n = nums2.length;
        for (int i = 0; i < Math.min(m, k); i++) {
            pq.offer(new int[]{i, 0});
        }
        while (k-- > 0 && !pq.isEmpty()) {
            int[] idxPair = pq.poll();
            List<Integer> list = new ArrayList<>();
            list.add(nums1[idxPair[0]]);
            list.add(nums2[idxPair[1]]);
            ans.add(list);
            if (idxPair[1] + 1 < n) {
                pq.offer(new int[]{idxPair[0], idxPair[1] + 1});
            }
        }
        return ans;
    }

}
```
