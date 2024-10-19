# 第k小的数对

## 腾讯

> - ***Question***
>   - 长度为 `N` 的数组 `arr` ，一定可以组成 `N * 2` 个数字对 `(x, y)` ，因为数字对的两个位置上可以自由选择 `arr` 中的任意一个数字（即自己和自己可以组成一个数字对）。请你返回所有数字对按照第一个位置从小到大，第一个位置一样第二个位置从小到大的规则排完序后的第 `k` 小的数字对。

## Java

> - ***改写快速排序***

```java
class Solution {
    
    public static int[] kthMinPair(int[] arr, int k) {
        // k不能超过数字对的个数
        if (k > arr.length * arr.length) {
            return null;
        }
        // 例如数组排完序后为 1 1 2 2 3 3 3 4 4 4 
        // 一共有100个数字对，请你找出第82小的数字对
        // 首先我们确定，以arr[i]位置作为数字对第一个位置的数字对的长度一定为arr.length个
        // 那么我们就可以找出第k小的数字对的第一个数字为4
        // 然后我们看以1 2 3作为第一个数字开头的数字对的个数有70个，那么第k小的数字对是以4做第一个的情况下从小到大排序的第12个
        // 然后我们分别看在确定第一个数字的情况下，数字对从小到大的状况，以0位置的1和1位置的1做第二个位置的情况分别有3种，2同理，找到12个，答案为（4，2）
        // 第一个位置的数字应该是谁，第二个参数用于求一个k应该对应哪个位置的数字
        int firstNum = getMinKth(arr, (k - 1) / arr.length);
        // 小于第一个数字的数字有多少个 7个小于4的
        int lessFirstNumSize = 0;
        // 第一个数字有多少个 3个4
        int firstNumSize = 0;
        // 遍历查找
        for (int num : arr) {
            if (num < firstNum) {
                lessFirstNumSize++;
            } else if (num == firstNum) {
                firstNumSize++;
            }
        }
        // 在第一个数字确定的情况下，排在第几个数对 12
        int rest = k - (lessFirstNumSize * arr.length);
        // 返回结果
        return new int[]{firstNum, getMinKth(arr, (rest - 1) / firstNumSize)};
    }
    
    // 改写快排，时间复杂度O(N)
    // 在无序数组arr中，找到，如果排序的话，arr[index]的数是什么？
    public static int getMinKth(int[] arr, int index) {
        int left = 0;
        int right = arr.length - 1;
        int pivot = 0;
        int[] range = null;
        while (left < right) {
            pivot = arr[left + (int) (Math.random() * (right - left + 1))];
            range = partition(arr, left, right, pivot);
            if (index < range[0]) {
                right = range[0] - 1;
            } else if (index > range[1]) {
                left = range[1] + 1;
            } else {
                return pivot;
            }
        }
        return arr[left];
    }
    
    public static int[] partition(int[] arr, int left, int right, int pivot) {
        int lessRight = left - 1;
        int moreLeft = right + 1;
        int cur = left;
        while (cur < moreLeft) {
            if (arr[cur] < pivot) {
                swap(arr, ++lessRight, cur++);
            } else if (arr[cur] > pivot) {
                swap(arr, cur, --moreLeft);
            } else {
                cur++;
            }
        }
        return new int[]{lessRight + 1, moreLeft - 1};
    }
    
    private static void swap(int[] arr, int i, int j) {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
    
}
```
