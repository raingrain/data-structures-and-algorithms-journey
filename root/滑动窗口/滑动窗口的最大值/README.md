### 题目

- [239. 滑动窗口最大值](https://leetcode.cn/problems/sliding-window-maximum/)
- [剑指 Offer 59 - I. 滑动窗口的最大值](https://leetcode.cn/problems/hua-dong-chuang-kou-de-zui-da-zhi-lcof/)

---

### 题意

![image](https://user-images.githubusercontent.com/75558694/175205477-c5b2e56e-4237-47a0-9691-5365d2aa83b8.png)

---

### Java实现

#### *思路*

> **- 暴力循环法**
>   - 暴力方法就是遍历数组中的所有窗口，然后在每个窗口中遍历寻找最大值，会超时
>
> ---
>
> **- 双端队列法**
>   1. 首先我们的双端队列需要自己处理窗口长度，我们采用检查过期元素的方式省去了对left的限制，只专注于right
>   2. 由于题目中窗口长度固定为k，不同于暴力方法中最开始的窗口是从0到k-1，我们通过判断什么时候往res中填答案来解决最开始队列不完整的问题（双端队列法从没有值开始扩张窗口），即只有当 `right >= k - 1` 时才开始往res中填答案

#### *代码*

```java
// java暴力循环或双端队列
class Solution {
    public static int[] maxSlidingWindow(int[] nums, int k) {
        return maxSlidingWindowByDeque(nums, k);
        // return maxSlidingWindowByViolence(nums, k);
    }

    // 双端队列实现
    public static int[] maxSlidingWindowByDeque(int[] nums, int k) {
        if (nums == null || k < 1 || nums.length < k) {
            return new int[0];
        }
        // 队列中存储的是下标，因为要移除窗口移动后过期的元素
        // 存值的话没法进行判断
        // 双端队列
        LinkedList<Integer> maxDeque = new LinkedList<>();
        // 结果数组，由题意其长度确定
        int[] res = new int[nums.length - k + 1];
        // 遍历res要的
        int index = 0;
        for (int right = 0; right < nums.length; right++) {
            // 当队列里面有值且nums[队尾元素]小于nums[right]，弹出队尾元素
            while (!maxDeque.isEmpty() && nums[maxDeque.getLast()] <= nums[right]) {
                maxDeque.removeLast();
            }
            // 先抛出队列中所有小于等于nums[right]的元素下标
            maxDeque.addLast(right);
            // 检查队首元素是不是国期元素，是的话就弹出队列
            if (maxDeque.getFirst() == right - k) {
                maxDeque.removeFirst();
            }
            // right >= k - 1是窗口在数组中完整出现的条件
            // 此时可以开始往res中填答案
            if (right >= k - 1) {
                // 双端队列的结构确保队首总是当前窗口的最大值
                res[index++] = nums[maxDeque.getFirst()];
            }
        }
        return res;
    }

    // 暴力循环
    public static int[] maxSlidingWindowByViolence(int[] nums, int k) {
        // if (nums == null || k < 1 || nums.length < k) {
        //     return null;
        // }
        int[] res = new int[nums.length - k + 1];
        int index = 0;
        int left = 0;
        int right = k - 1;
        while (right < nums.length) {
            res[index] = nums[left];
            for (int i = left + 1; i <= right; i++) {
                res[index] = Math.max(res[index], nums[i]);
            }
            index++; 
            left++;
            right++;
        }
        return res;
    }
}
```

---

### JavaScript重写

#### *思路*

> 参考**Java实现**

#### *代码*

```javascript
/**
 * @param {number[]} nums
 * @param {number} k
 * @return {number[]}
 */
var maxSlidingWindow = function(nums, k) {
    return maxSlidingWindowByDeque(nums, k);
    // return maxSlidingWindowByViolence(nums, k);
};

function maxSlidingWindowByDeque(nums, k) {
    if (nums == null || k < 1 || nums.length < k) {
        return [];
    } else {
        const maxDeque = [];
        const res = [];
        let index = 0;
        for (let right = 0; right < nums.length; right++) {
            while (maxDeque.length !== 0 && nums[maxDeque[maxDeque.length - 1]] <= nums[right]) {
                maxDeque.pop();
            }
            maxDeque.push(right);
            if (maxDeque[0] === right - k) {
                maxDeque.shift();
            }
            if (right >= k - 1) {
                res[index++] = nums[maxDeque[0]];
            }
        }
        return res;
    }
}

function maxSlidingWindowByViolence (nums, k) {
    let res = [];
    let index = 0;
    let left = 0;
    let right = k - 1; 
    while (right < nums.length) {
        res[index] = nums[left];
        for (let i = left + 1; i <= right; i++) {
            res[index] = Math.max(res[index], nums[i]);
        }
        index++; 
        left++;
        right++;
    }
    return res;
}
```
