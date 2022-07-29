# [剑指 Offer 51. 数组中的逆序对](https://leetcode-cn.com/problems/shu-zu-zhong-de-ni-xu-dui-lcof/)

### 解题思路
1. 暴力搜索的时间复杂度是O(N^2)，用归并排序处理的话是O(N^logN)
2. 其实代码没有几处变动，关键是要理解题目在干什么
3. 逆序对本质上是求对于一个数来说，其左边的数有几个比它大，或者说右边的数有几个比它小，选择哪种思路决定了你在关键代码上的细节处理是怎么样的，下面就是两种思路的分别实现


### 代码

```java
// java改写归并排序递归版
class Solution {
    public int reversePairs(int[] nums) {
        if (nums == null || nums.length < 2) {
            return 0;
        } else if (nums.length == 2) {
            return nums[0] > nums[1] ? 1 : 0;
        } else {
            return process(nums, 0, nums.length - 1);
        }
    }

    public int process(int[] arr, int left, int right) {
        if (left == right) {
            return 0;
        }
        int mid = left + ((right - left) >> 1);
        return process(arr, left, mid) + process(arr, mid + 1, right) + merge(arr, left, mid, right);
    }

    public int merge(int[] arr, int left, int mid, int right) {
        int[] helpArr = new int[right - left + 1];
        int leftPoint = left;
        int rightPoint = mid + 1;
        int count = 0;
        int ans = 0;
        while ((leftPoint <= mid) && (rightPoint <= right)) {
            // 找对于右边的数字来说，左边的数及其之后的有多少个比它大的
            // 那么在这个左子区域上的能够与当前这个右组数构成逆序对的个数就找到了，因为当前leftPoint比rightPoint大，而left到mid是有序的，自然leftPoint后面的数都能与当前rightPoint构成逆序对
            // 对于rightPoint来说，其左边的数的一小部分就被检查（检查能不能与之构成逆序对）完毕
            ans += arr[leftPoint] > arr[rightPoint] ? (mid - leftPoint + 1) : 0;
            helpArr[count++] = arr[leftPoint] <= arr[rightPoint] ? arr[leftPoint++] : arr[rightPoint++] ;
        }
        while (leftPoint <= mid) {
            helpArr[count++] = arr[leftPoint++];
        }
        while (rightPoint <= right) {
            helpArr[count++] = arr[rightPoint++];
        }
        for (int i = 0; i < helpArr.length; i++) {
            arr[left + i] = helpArr[i];
        }
        return ans;
    }
}
```

```java
// java改写归并排序递归版
class Solution {
    public int reversePairs(int[] nums) {
        if (nums == null || nums.length < 2) {
            return 0;
        } else if (nums.length == 2) {
            return nums[0] > nums[1] ? 1 : 0;
        } else {
            return process(nums, 0, nums.length - 1);
        }
    }

    public int process(int[] arr, int left, int right) {
        if (left == right) {
            return 0;
        }
        int mid = left + ((right - left) >> 1);
        return process(arr, left, mid) + process(arr, mid + 1, right) + merge(arr, left, mid, right);
    }

    public int merge(int[] arr, int left, int mid, int right) {
        int[] helpArr = new int[right - left + 1];
        int leftPoint = mid;
        int rightPoint = right;
        int count = helpArr.length - 1;
        int ans = 0;
        while ((leftPoint >= left) && (rightPoint > mid)) {
            // 同理，换个方向罢了
            ans += arr[leftPoint] > arr[rightPoint] ? (rightPoint - mid) : 0;
            helpArr[count--] = arr[leftPoint] > arr[rightPoint] ? arr[leftPoint--] : arr[rightPoint--];
        }
        while (leftPoint >= left) {
            helpArr[count--] = arr[leftPoint--];
        }
        while (rightPoint > mid) {
            helpArr[count--] = arr[rightPoint--];
        }
        for (int i = 0; i < helpArr.length; i++) {
            arr[left + i] = helpArr[i];
        }
        return ans;
    }
}
```
