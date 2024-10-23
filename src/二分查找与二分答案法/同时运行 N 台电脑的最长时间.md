# 同时运行 N 台电脑的最长时间

## [2141. 同时运行 N 台电脑的最长时间](https://leetcode.cn/problems/maximum-running-time-of-n-computers/)

> - **Question**
>   - 你有 `n` 台电脑。给你整数 `n` 和一个下标从 `0` 开始的整数数组 `batteries` ，其中第 `i` 个电池可以让一台电脑运行 `batteries[i]` 分钟。你想使用这些电池让全部 `n` 台电脑同时运行。
>   - 一开始，你可以给每台电脑连接至多一个电池。然后在任意整数时刻，你都可以将一台电脑与它的电池断开连接，并连接另一个电池，你可以进行这个操作任意次。新连接的电池可以是一个全新的电池，也可以是别的电脑用过的电池。断开连接和连接新的电池不会花费任何时间。
>   - 注意，你不能给电池充电。请你返回你可以让 `n` 台电脑同时运行的最长分钟数。
>   - **Tips**
>     - `1 <= n <= batteries.length <= 10^5`
>     - `1 <= batteries[i] <= 10^9`

## Java

> - **二分查找**

```java
class Solution {

    public long maxRunTime(int n, int[] batteries) {
        // 对电量数组进行排序
        Arrays.sort(batteries);
        // 给出前缀和数组用于求电量小于多少的电池的累加和
        long[] prefixSum = new long[batteries.length];
        prefixSum[0] = batteries[0];
        for (int i = 1; i < batteries.length; i++) {
            prefixSum[i] = prefixSum[i - 1] + batteries[i];
        }
        // 二分查找，找最大的供电时间
        long left = 0;
        long mid = 0;
        // 和下面判断单个电池电量小于单个大楼所需电量的情况一样
        // 最好情况就总和除以楼数
        long right = prefixSum[batteries.length - 1] / n;
        long ans = -1;
        while (left <= right) {
            mid = (left + right) / 2;
            if (ok(batteries, prefixSum, mid, n)) {
                // 找最大轮数
                ans = mid;
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return ans;
    }

    // 假设要撑10分钟，返回能不能撑
    // 电量大于等于10的一直给某些楼供电
    // 剩下的楼所需要的总供电数和剩下电池的总电量
    // 总电量大于总供电数就能确定剩下的电池能让剩下的大楼撑10分钟
    public boolean ok(int[] arr, long[] prefixSum, long time, int num) {
        int l = 0;
        int m = 0;
        int r = arr.length - 1;
        int left = arr.length;
        // >= time 最左
        while (l <= r) {
            m = (l + r) / 2;
            if (arr[m] >= time) {
                left = m;
                r = m - 1;
            } else {
                l = m + 1;
            }
        }
        num -= arr.length - left;
        long rest = left == 0 ? 0 : prefixSum[left - 1];
        return time * (long) num <= rest;
    }
}
```
