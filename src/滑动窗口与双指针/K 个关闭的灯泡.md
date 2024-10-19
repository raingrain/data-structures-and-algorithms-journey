# K 个关闭的灯泡

## [683. K 个关闭的灯泡](https://leetcode.cn/problems/k-empty-slots/)

> - ***Question***
>   - 给定一个数组表示 `i` 天开 `bulbs[i]`  号灯泡，再给定整数 `k` 。
>   - 有多少天就有多少个灯泡，每天打开的灯泡都不一样。
>   - 返回哪两个编号差为 `k` 的灯泡在都打开的时刻（打开时刻晚的灯泡在打开的时刻），它们中间的 `k` 个灯泡（编号中间）都没打开，这个时刻最早是多少。

---

## *Java*

> - ***由滑动窗口启发而来的三指针***

```java
class Solution {
    
    public static int kEmptySlots2(int[] bulbs, int k) {
        // days[i]表示第i号灯泡第days[i]天开
        int[] days = new int[bulbs.length];
        for (int i = 0; i < bulbs.length; i++) {
            days[bulbs[i] - 1] = i + 1;
        }
        int ans = Integer.MAX_VALUE;
        // 若k=3，假设当前是如下情况
        // 2 9 8 7 4 要开的天数
        // 4 5 6 7 8 灯泡号
        // left和right指针维护一个长度为k+2的区域
        // 中间指针从left+1开始遍历知道到达right
        // 中间出现小于等于left和right的开启时间的停止遍历中间区域（除非撞上right），left跳到mid，后面随之动，遍历到了收集答案进行比较
        // 注意，遍历到了我们可以推出中间区域所有灯都不满足题目要求，因为right在作为中间区域时违规，因为当初的中间区域大于right才使right合法
        for (int left = 0, mid = 1, right = k + 1; right < bulbs.length; mid++) {
            // 验证指针mid
            // mid 永远不和left撞上的！
            // 1) mid在left和right中间验证的时候，没通过！
            // 2) mid是撞上right的时候
            if (days[mid] <= Math.max(days[left], days[right])) {
                if (mid == right) { // 收答案！
                    ans = Math.min(ans, Math.max(days[left], days[right]));
                }
                left = mid;
                right = mid + k + 1;
            }
        }
        return (ans == Integer.MAX_VALUE) ? -1 : ans;
    }
    
}
```
