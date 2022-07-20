# [215. 数组中的第K个最大元素](https://leetcode.cn/problems/kth-largest-element-in-an-array/)

# [剑指 Offer II 076. 数组中的第 k 大的数字](https://leetcode.cn/problems/xx4gT2/)

![image](https://user-images.githubusercontent.com/75558694/179694911-8bc1da4d-d8a0-44aa-8622-38b8dbe90f9e.png)

---

### *Java*

> - ***大根堆***
>   1. 先把数组中的前k个数加入到堆中；
>   2. 然后对于剩下的数，比堆中最大的数（堆顶）小的就加入到堆中并把原来的堆顶弹出，遍历完一遍数组后的堆顶就是第k小。
>   
> - ***快速排序递归or迭代***
>   - 由于快排的每次分治都会返回等于区的左右边界，而在一个有序数组中第k小的位置是确定的并且快排从中间往两边有序，所以我们可以利用等于区来找到第k小的数，当k - 1落在等于区时就返回等于区的值。
>   
> - ***bfprt算法 + 快速排序迭代***
>   - 只对于怎么选择基准值作为等于区的值做了改进：把数组分为多个长度为5的小段，不足5的自成一段，把每一段的中位数拿出来作为一个中位数数组，在选择这个中位数数组的中位数作为基准值，这样确保了每一次排序等于区左右两边的数的数量在3/10N到7/10N之间，解决了随机挑选基准值带来的极端情况

```java
class Solution {

    // 从findKth到TOPK & MinK
    // 直接遍历一遍的时间复杂度再找是O(N*logN)，自底向上建堆再遍历一遍的时间复杂度是O(N+K*logN)，用bfprt+快排搞就是O(N+K*logK)
    // 给定一个整数数组A（数组中元素为互不相同的整数）及它的大小n，同时给定k，请返回其中最小的k个数，顺序与原数组中元素顺序一致。
    public int[] findKthNumbers(int[] A, int n, int k) {
        // 不要求顺序互不相同可以不用
        int[] B = A.clone();
        int KthLeast = findKthLeast(A, k);
        int[] ans = new int[k];
        int index = 0;
        // 由于数组中元素互不相同，我们可以一遍遍历过
        // 如果数组中有相同元素，我们需要先遍历一遍计算出比第k小的数的值小的数有几个，假定k为100，找到了90个比第k小的数小的数，那么剩下10个全为k
        // 再遍历一遍，只取前10个k即可
        for (int i = 0; i < B.length; i++) {
            if (B[i] <= KthLeast) {
                ans[index++] = B[i];
            }
        }
        return ans;
        // write code here
    }

    // 找到第k大的数 -> 找到第len-k+1小的数字
    public static int findKthLargest(int[] arr, int k) {
        return findKthLeast(arr, arr.length - k + 1);
    }

    // 找到第k小的数字
    public static int findKthLeast(int[] arr, int k) {
        // return quickSortBybfprt(arr, 0, arr.length - 1, k - 1);
        return quickSortByLoop(arr, 0, arr.length - 1, k - 1);
        // return quickSortByRecursion(arr, 0, arr.length - 1, k - 1);
        // return kthMinNumberByMaxHead(arr, k);
    }

    // 基于bfprt改进的快速排序迭代版
    public static int quickSortBybfprt(int[] arr, int left, int right, int index) {
        int pivot = 0;
        int[] range = null;
        while (left < right) {
            // bfprt的核心在于怎么选基准值
            pivot = medianOfMedians(arr, left, right);
            // pivot= arr[left + (int) (Math.random() * (right - left + 1))];
            range = partition(arr, left, right, pivot);
            if (index >= range[0] && index <= range[1]) {
                return pivot;
            } else if (index < range[0]) {
                right = range[0] - 1;
            } else {
                left = range[1] + 1;
            }
        }
        return arr[left];
    }

    // 获取中位数数组的中位数作为快排的基准值
    public static int medianOfMedians(int[] arr, int left, int right) {
        // 需要考虑的长度
        int len = right - left + 1;
        // 是否能完整的切成每份长度为5
        int offset = len % 5 == 0 ? 0 : 1;
        // 每一份的中位数组成的数组
        int[] medians = new int[len / 5 + offset];
        for (int i = 0; i < medians.length; i++) {
            // L代表每一份在arr中的开头位置
            int L = left + i * 5;
            // 有可能L+4会越界
            medians[i] = getMedian(arr, L, Math.min(right, L + 4));
        }
        // 让bfprt帮我找到我的中位数
        return quickSortBybfprt(medians, 0, medians.length - 1, medians.length / 2);
    }

    // 找到一个数组的中位数
    public static int getMedian(int[] arr, int left, int right) {
        insertionSort(arr, left, right);
        return arr[(left + right) / 2];
    }

    // 小范围的数组排序选常数时间最小的插入排序
    public static void insertionSort(int[] arr, int left, int right) {
        for (int i = left + 1; i <= right; i++) {
            for (int j = i - 1; j >= left && arr[j] > arr[j + 1]; j--) {
                swap(arr, j, j + 1);
            }
        }
    }

    // 迭代版快速排序
    public static int quickSortByLoop(int[] arr, int left, int right, int index) {
        int pivot = 0;
        int[] range = null;
        while (left < right) {
            pivot = arr[left + (int) (Math.random() * (right - left + 1))];
            range = partition(arr, left, right, pivot);
            if (index >= range[0] && index <= range[1]) {
                return pivot;
            } else if (index < range[0]) {
                right = range[0] - 1;
            } else {
                left = range[1] + 1;
            }
        }
        return arr[left];
    }

    // 假定arr有序，即查找第index+1小的数字 -> arr[left-right]中查找arr中第index位置的数
    public static int quickSortByRecursion(int[] arr, int left, int right, int index) {
        if (left == right) {
            return arr[left];
        } else {
            // 随机选一个
            int pivot = arr[left + (int) (Math.random() * (right - left + 1))];
            int[] range = partition(arr, left, right, pivot);
            if (index >= range[0] && index <= range[1]) {
                return arr[index];
            } else if (index < range[0]) {
                // 我在小于区
                return quickSortByRecursion(arr, left, range[0] - 1, index);
            } else {
                // 我在大于区
                return quickSortByRecursion(arr, range[1] + 1, right, index);
            }
        }
    }

    // 荷兰国旗问题
    public static int[] partition(int[] arr, int left, int right, int pivot) {
        // 小于区的右边界
        int lessR = left - 1;
        // 大于区的左边界
        int moreL = right + 1;
        // 迭代指针
        int index = left;
        while (index < moreL) {
            if (arr[index] < pivot) {
                // 小于就交换位置且小于区右括，继续遍历
                swap(arr, ++lessR, index++);
            } else if (arr[index] > pivot) {
                // 大于就交换位置且大于区左括，指针停止
                swap(arr, --moreL, index);
            } else {
                // 等于就不交换，遍历下一个
                index++;
            }
        }
        return new int[]{lessR + 1, moreL - 1};
    }

    // 交换数组中的两个数
    public static void swap(int[] arr, int i, int j) {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }

    // 大根堆
    public static int kthMinNumberByMaxHead(int[] arr, int k) {
        PriorityQueue<Integer> maxHead = new PriorityQueue<>(new MaxHeapComparator());
        // 构建出大小为k的一个大根堆
        for (int i = 0; i < k; i++) {
            maxHead.add(arr[i]);
        }
        // 剩下的数有比堆顶小的就弹出堆顶，并加入这个数
        for (int i = k; i < arr.length; i++) {
            if (arr[i] < maxHead.peek()) {
                maxHead.poll();
                maxHead.add(arr[i]);
            }
        }
        // 遍历完数组后，这个大根堆的堆顶一定是第k小的数
        return maxHead.peek();
    }

    public static class MaxHeapComparator implements Comparator<Integer> {

        // 大根堆需要的降序排序
        public int compare(Integer o1, Integer o2) {
            return o2 - o1;
        }
    }
}
```

---
