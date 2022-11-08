# 最大值减去最小值小于或等于num的子数组数量

## 程序员代码面试指南：IT名企算法与数据结构题目最优解（第二版） 第1章 栈和队列 最大值减去最小值小于或等于num的子数组数量

> - ***Question***
>   - 给定一个数组arr和一个正整数 `num` 。
>   - 定义 `arr` 的某个子数组 `sub` 达标的条件为子数组中的最大值减去最小值小于等于 `num` 。
>   - 请返回 `arr` 中达标子数组的数量。

---

## *Java*

> - ***双端队列实现窗口内最值的更新***
>   - 首先明白两个结论：
>     - 一个子数组达标，它的所有子数组也达标。
>     - 一个子数组不达标，它的所有父数组也不达标。
>   - 准备两个双端队列实现窗口最大最小值的更新。
>   - 先把 `left` 固定在 `0` ，然后开始循环：
>     - 如果 `left` 到 `right` 上达标， `right` 就右移并更新窗口内最值。
>     - 否则就 `break` ，结果中加上 `right` 减去 `left` （从 `left` 到 `right-1` 范围上的所有以 `left` 开头的子数组达标），并在下一次 `left` 右移前检查过期元素。
>   - 时间复杂度为 `O(N)` 。

```java
class Solution {
    
    public static void main(String[] args) {
        int[] arr = new int[]{1, 3, -1, -3, 5, 3, 6, 7};
        System.out.println(byDeque(arr, 3));
    }
    
    private static int byDeque(int[] arr, int num) {
        if (arr == null || arr.length == 0 || num <= 0) {
            return 0;
        }
        int ans = 0;
        Deque<Integer> maxDeque = new ArrayDeque<>();
        Deque<Integer> minDeque = new ArrayDeque<>();
        int right = 0;
        // 遍历[0...) [1...) [2...)
        for (int left = 0; left < arr.length; ++left) {
            while (right < arr.length) {
                // 更新最值
                while (!maxDeque.isEmpty() && arr[maxDeque.getLast()] <= arr[right]) {
                    maxDeque.pollLast();
                }
                maxDeque.offerLast(right);
                while (!minDeque.isEmpty() && arr[minDeque.getLast()] >= arr[left]) {
                    minDeque.pollLast();
                }
                minDeque.offerLast(right);
                // 判断[left, right]上是不是达标的
                if (arr[maxDeque.getFirst()] - arr[minDeque.getFirst()] > num) {
                    // 一个子数组不达标，它的所有右边比它大的父数组也不达标
                    break;
                } else {
                    ++right;
                }
            }
            // 一个子数组达标，它的所有以左边界为开头的子数组也达标
            ans += (right - left);
            if (maxDeque.getFirst() == left) {
                maxDeque.pollFirst();
            }
            // 检查有没有最值在left上，移除过期元素
            if (minDeque.getFirst() == left) {
                minDeque.pollFirst();
            }
        }
        return ans;
    }
    
}
```

---

> ***last change: 2022/11/8***

---
