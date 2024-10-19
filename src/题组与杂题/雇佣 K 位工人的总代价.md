# 雇佣 K 位工人的总代价

## [2462. 雇佣 K 位工人的总代价](https://leetcode.cn/problems/total-cost-to-hire-k-workers/)

> - ***Question***
>   - 给你一个下标从 `0` 开始的整数数组 `costs` ，其中 `costs[i]` 是雇佣第 `i` 位工人的代价。
>     - 同时给你两个整数 `k` 和 `candidates` 。我们想根据以下规则恰好雇佣 `k` 位工人：
>     - 总共进行 `k` 轮雇佣，且每一轮恰好雇佣一位工人。
>       - 在每一轮雇佣中，从最前面 `candidates` 和最后面 `candidates` 人中选出代价最小的一位工人，如果有多位代价相同且最小的工人，选择下标更小的一位工人。
>       - 比方说 `costs = [3, 2, 7, 7, 1, 2]` 且 `candidates = 2` ，第一轮雇佣中，我们选择第 `4` 位工人，因为他的代价最小 `[3, 2, 7, 7, 1, 2]` 。
>     - 第二轮雇佣，我们选择第 `1` 位工人，因为他们的代价与第 `4` 位工人一样都是最小代价，而且下标更小， `[3, 2, 7, 7, 2]` 。注意每一轮雇佣后，剩余工人的下标可能会发生变化。
>     - 如果剩余员工数目不足 `candidates` 人，那么下一轮雇佣他们中代价最小的一人，如果有多位代价相同且最小的工人，选择下标更小的一位工人。
>     - 一位工人只能被选择一次。
>   - 返回雇佣恰好 `k` 位工人的总代价。
>   - ***tips:***
>     - `1 <= costs.length <= 10^5`
>     - `1 <= costs[i] <= 10^5`
>     - `1 <= k, candidates <= costs.length`

---

## *Java*

> - ***堆 + 双指针***
>   - 选择工人的策略就是不断的在一定数量的人中选择 `costs` 最小的，显然这需要用到最小堆。
>   - 然后需要处理的就是如何往堆中添加候选人，因为是从最前面 `candidiates` 个和最后面 `candidates` 个，所以用对撞指针来标记一下就要可以了。
>   - 初始状态先把前 `candidates` 个和后 `candidates` 个添加到堆中。每次选择后，再看是选的前面的，还是后面，然后把对应的指针向前推进一个即可。

```java
import java.util.*;

class Solution {

    public long totalCost(int[] costs, int k, int candidates) {
        int n = costs.length;
        PriorityQueue<Integer> minHeap = new PriorityQueue<>((i, j) -> {
            if (costs[i] == costs[j]) {
                return i - j;
            }
            return costs[i] - costs[j];
        });
        int left = 0;
        int right = n - 1;
        for (; left < candidates && left <= right; left++, right--) {
            minHeap.offer(left);
            if (left < right) {
                minHeap.offer(right);
            }
        }

        long ans = 0L;
        while (k-- > 0) {
            int x = minHeap.poll();
            ans += (long) costs[x];

            if (left < right) {
                if (x > left) {
                    minHeap.offer(right);
                    right--;
                } else {
                    minHeap.offer(left);
                    left++;
                }
            } else if (left == right) {
                minHeap.offer(left);
                left++;
            }
        }

        return ans;
    }

}
```

---

> ***last change: 2024/3/29***

---
